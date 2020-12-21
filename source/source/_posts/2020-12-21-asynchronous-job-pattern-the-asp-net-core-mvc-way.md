---
title: Asynchronous Job Pattern - The ASP.NET Core MVC Way
tags:
  - microservices
  - advanced
  - dotnet
categories:
  - blog
disqusId: asynchronous-job-pattern-the-asp-net-core-mvc-way
date: 2020-12-21 21:33:44
---

{% asset_img calendar.jpg Advent calendar %}

Let me present a pattern we used to solve a problem haunting our enterprise application for ages. It was initially designed to handle long running operations, using [RESTful approach](http://restalk-patterns.org/long-running-operation-polling.html), but we soon realized it could be used in many other ways. As it generalizes the system of submitting requests and obtaining responses, it can easily be adapted to different business processes, while keeping unified client interface.

<!-- more -->

_This post is part of [C# Advent Calendar 2020](https://www.csadvent.christmas/). Cheers to [Matthew D. Groves](https://twitter.com/mgroves) for letting me participate!_

## Building blocks

From a _client_ perspective, the pattern usage is pretty simple: it submits a _job_ for processing to an _endpoint_; _job_ status is polled until processing is finished; if status was successful, client uses _endpoint_ to obtain output.

On server side, several components are required to achieve such functionality. Lets take a look at the big picture and define individual pattern pieces.

### Job

A _job_ is a principal entity, representing _client_ intent processed by a designated _worker_. It is composed of:

* **Header**, containing job metadata such as unique identifier, current status, time of creation/start/finish, potential issues as well as estimated time of completion
* **Input parameters**
* **Output values**

### Worker

Component that does heavy lifting. Main responsibility is actual processing, as each _worker_ is able to handle specific _job_ type. In simple terms, we can describe it as a function accepting input parameters and returning either _job_ output or an error.

### Endpoint

Serves as an entry-point for _clients_. After creating a job, _client_ should be able to obtain _job_ status at any point in time, as well as output after processing is finished. _Endpoint_ should also allow _client_ to cancel and clean-up a _job_. Hence, it needs to provide following services:

* Create _job_
* Get _job_ status
* Get _job_ output
* Delete _job_

### Job Repository and Queue

A _repository_ will be used to store all _jobs_, which would allow both _endpoint_ and _worker_ to read and update _job_ state. For now, let's consider _repository_ an abstraction, as concrete implementation and storage technology may vary, depending on usage scenario.

Binding between an _endpoint_ and a _worker_ would be achieved using a _job queue_ concept. It has to provide a _producer_ and a _consumer_, depending on the component using _queue_ services. Similar to how _repository_ was defined, implementation may range from memory one to a hyper-scale message broker. 

Final pattern architecture is depicted in the diagram bellow. As all core components are defined, we should move on to implementation using APS.NET Core.

![Job pattern architecture](architecture.png)

## ASP.NET Core implementation

### Implementation considerations

In order to make the implementation as universal as possible, we need to limit _job_ inputs and outputs to one of each. If multiple values are expected, they could be provided via a custom model. `JobExecutionResult` will serve as a wrapper for output value, containing result of _worker_ execution and any possible issues. If we define them as types `TInput` and `TOutput`, our _worker_ and _endpoint_ could look something like this:

<script src="https://gist.github.com/uveta/85943b7354871239058c4b45ffca8ee9.js"></script>
<script src="https://gist.github.com/uveta/9ebe4d6514c8bf6d22dca908eeeb0c04.js"></script>

As _endpoint_ is user independent concept, it will be up to pattern to provide the implementation. On the other hand, _worker_ is responsible for executing custom actions, hence it's implementation has to be provided by the pattern user. This reasoning will come into play during design, as we would like to provide plugin architecture for individual pattern components. This will allow utilizing only building blocks required for given scenario, while the user would provide implementation of required and customizable parts.

### Plugging in worker implementation

_Worker_ flow is straightforward: it should use _queue consumer_ to listen for incoming _jobs_; whenever one is received, it should process it and update _job_ status and output via _repository_; then it waits for next one and repeats previous steps. This cycle is supposed to run for the whole application lifetime, as new _jobs_ can arrive at any time. A natural solution to implement such functionality are [hosted services](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/host/hosted-services). In our case, we could extract all common code (starting _queue consumer_, input/output serialization and _job_ update) to a `WorkerInvoker<TWorker>` hosted service, bound to specific _worker_ by it generic type `TWorker`.

Adding `TWorker` type to DI container has multiple benefits. On one side, it can be injected into `WorkerInvoker<TWorker>`, removing the need to create it manually. On the other hand, it allows developers of `TWorker` to freely inject business services into it.

### Exposing endpoints

Since controllers are used as primary entry point into MVC applications, they are an obvious choice for coupling with _endpoints_. In this case, [initial description](http://restalk-patterns.org/long-running-operation-polling.html) should be followed to the letter, as we are in domain of HTTP REST operations. Hence, controllers should include following routes:

* **POST _/jobs_**: allows creating new jobs; responds with status _202 (Accepted)_, containing _job_ resource _URL_
* **GET _/jobs/{jobId}_**: resource _URL_ can be used for polling _job_ status, while in progress; when processing is finished, use _303 (See Other)_ redirect to provide client with an output resource _URL_
* **GET _/jobs/{jobId}/output_**: output resource _URL_; should return _200 (OK)_ result containing output value, after _job_ processing finishes; returns _404 (NotFound)_ error otherwise
* **DELETE _/jobs/{jobId}_**: disposes of any reserved resources; client should call it after polling finishes and output is obtained; if not done by client, server should clean up old _jobs_ automatically

The burden of creating a controller should not fall on the user; it is the responsibility of the pattern itself, since each route functionality is known in advance. For this purpose we could implement a generic `JobsController<TEndpoint>`, which would be bound to specific _endpoint_ via its type parameter `TEndpoint`.

As multiple _endpoints_ per service have to be supported, MVC requires providing different underlying type for each controller. This is not possible with our idea of using a generic one, as changing generic's type parameter does not change overall generic type. Instead, we should create individual controller type from base generic dynamically, using a bit of [TypeBuilder](https://docs.microsoft.com/en-us/dotnet/api/system.reflection.emit.typebuilder) reflection magic. Generated types could then be added to MVC using custom [IApplicationPartTypeProvider](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.iapplicationparttypeprovider).

### Few words on repository and queue

Following how we defined _job repository_ and _queue_, respecting interfaces would be:

<script src="https://gist.github.com/uveta/44ca3dfbba05536e5091fbc353b611ec.js"></script>
<script src="https://gist.github.com/uveta/585866e8ae67e75d7c65eac9cccb6d0b.js"></script>

The pattern has to supply default memory implementation for both of them. Although this should suffice for test and single-service usage, it completely falls flat in any advanced scenarios. Hence, replacing default implementations must be allowed via extensions.

### Configuring job services

We should follow principals ASP.NET Core was build upon and adopt a plugin architecture in order to configure our pattern services. I used [a sample project](https://github.com/uveta/extensions-jobs/tree/main/samples/MvcDemo) to demonstrate how such behavior could be achieved. The following example includes definition of one _endpoint_ and its bound _worker_.

<script src="https://gist.github.com/uveta/777c12716df3015ebc67a651916bea23.js"></script>

In this case, `PingRequest` and `PingResponse` correspond to input and output types. `PingWorker` represents a custom implementation of `IWorker`, supplied by user. Other components (_endpoint_, _repository_ and _queue_) are provided by pattern and only configured here.

## Usage alternatives

Proposed implementation is just one of many possible, as well as its usage. All building blocks are introduced as abstract concepts, which can be adapted for different scenarios than initially intended. For example, an _endpoint_ could be utilized to issue an RPC call without knowing the address of remote service; client would use _endpoint_ to create a new job, which would reach designated _worker_ via queue, effectively removing the need of individual services knowing other ones even exist. Only constraint is imposed by specific business feature, having strictly defined input and output.

Further improvements can be introduced on _workers_ as well. In order to use them in a real-world scenarios, there has to be a possibility of configuring their maximum scaling and execution period limits. This feature will have to be supported by _queue_ implementation, as _worker_ is invoked based on actions triggered by _queue consumer_.

Finally, default memory implementations of _queue_ and _repository_ are only good for [demonstration purposes](https://github.com/uveta/extensions-jobs/tree/main/samples/MvcDemo). _Repository_ should offer some form of permanent external storage, especially if usage in multi-service applications is considered. Same goes for _queue_, as the need for underlying message broker is evident in even simple production scenarios.

## Conclusion

In this article I explained which set of problems did we solve using long running job pattern. We also saw one of the ways to implement it, using ASP.NET Core MVC. As a final observation, I described how inner pattern components can be used outside of MVC, in any system that wants to generalize request/response flow and reduce service coupling.

Source code and samples can be found on [Github](https://github.com/uveta/extensions-jobs). Individual packages have also been published to [nuget.org](https://www.nuget.org/packages/Uveta.Extensions.Jobs/), in case you would like to try them in your own application.
