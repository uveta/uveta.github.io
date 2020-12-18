---
title: Asynchronous Job Pattern - The ASP.NET Core MVC Way
tags:
  - microservices
  - advanced
  - dotnet
categories:
  - blog
disqusId: asynchronous-job-pattern-the-asp-net-core-mvc-way
---

abc abc abc

<!-- more -->

_This post is part of [C# Advent Calendar 2020](https://www.csadvent.christmas/). Cheers to [Matthew D. Groves](https://twitter.com/mgroves) for letting me participate!_

created from the need to run long running operations using RESTful operations [the pattern](http://restalk-patterns.org/long-running-operation-polling.html). But it could be used in many other ways, as it generalizes the process of submitting requests. With some consideration, the pattern can be extended to provide a scalable solution needed by micro-service architectures.  

## Building blocks

### Job

A _job_ is a principal entity, representing _client_ intent to be processed by its designated _worker_. It consists of:

* header, containing job metadata such as unique identifier, current status, time of creation/start/finish, potential issues as well as estimated time of completion
* input parameters
* output values

### Worker

Component that does heavy lifting. Main responsibility is actual processing, as each _worker_ is able to handle certain type of _jobs_. In simple terms, we can describe it as a function accepting input parameters and returning either _job_ output values or an error.

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

Final version can be found on [Github](https://github.com/uveta/extensions-jobs).

### Implementation considerations

In order to make the implementation as universal as possible, we need to limit _job_ inputs and outputs to one of each. If multiple values are expected, they could be provided via a custom model. _JobExecutionResult_ will serve as a wrapper for output value, containing result of _worker_ execution and any possible issues. If we define them as types _TInput_ and _TOutput_, our _worker_ and _endpoint_ could look something like this:

<script src="https://gist.github.com/uveta/85943b7354871239058c4b45ffca8ee9.js"></script>
<script src="https://gist.github.com/uveta/9ebe4d6514c8bf6d22dca908eeeb0c04.js"></script>

As _endpoint_ is user independent concept, it will be up to pattern to provide the implementation. On the other hand, _worker_ is responsible for executing custom actions, hence it's implementation has to be provided by the patter user. This reasoning will come into play during design, as we would like to provide plugin architecture for individual pattern components. This will allow utilizing only building blocks required for given scenario, while the user would provide implementation of required and customizable parts.

### Plugging in worker implementation

The role of a _worker_ is simple: it should use _queue consumer_ to listen for incoming jobs; whenever one is received, it should process it and update _job_ status and output via _repository_; then it has to wait for the next one... This cycle is supposed to run for the whole application lifetime, as new _jobs_ can arrive at any time. A natural solution to implement such functionality are [hosted services](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/host/hosted-services). In our case, we could extract all common code (starting _queue consumer_, input/output serialization and _job_ update) to a _WorkerInvoker&lt;TWorker&gt;_ hosted service, bound to specific _worker_ by it generic type _TWorker_.

Adding _TWorker_ type to DI container has multiple benefits. On one side, it can be injected into _WorkerInvoker&lt;TWorker&gt;_, removing the need to create it manually. On the other hand, it allows developers of _TWorker_ to freely inject business services into it.

### Exposing endpoints

As controllers are used as primary entry point into MVC applications, they are an obvious choice for coupling with _endpoints_. Implemented controller should follow [initial definition](http://restalk-patterns.org/long-running-operation-polling.html) to the letter. Following routes should be exposed:

* **POST _/jobs_**: allows creating new jobs; responds with status _202 (Accepted)_, containing _job_ resource _URL_
* **GET _/jobs/{jobId}_**: resource _URL_ can be used for polling _job_ status, while in progress; when processing is finished, use _303 (See Other)_ redirect to provide client with an output resource _URL_
* **GET _/jobs/{jobId}/output_**: output resource _URL_; should return _200 (OK)_ result containing output value, after _job_ processing finishes; returns _404 (NotFound)_ error otherwise
* **DELETE _/jobs/{jobId}_**: disposes of any reserved resources; client should call it after polling finishes and output is obtained; if not done by client, server should clean up old _jobs_ automatically

The burden of creating a controller should not fall on the user; it is the responsibility of the pattern itself, since all of the routes behavior is known in advance. For this purpose we should implement a generic _JobsController&lt;TEndpoint&gt; which would be bound to specific _endpoint_ via its type parameter _TEndpoint_.

Since we would like to support multiple _endpoints_ per service, we would have to provide different type for each controller, as it is required by MVC. This is not possible with our idea of using a generic controller, as changing generic's type parameter does not change overall generic type. Instead, we should create individual controllers from base generic dynamically, using a bit of [TypeBuilder](https://docs.microsoft.com/en-us/dotnet/api/system.reflection.emit.typebuilder) reflection magic. Generated types could then be added to MVC using custom [IApplicationPartTypeProvider](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.iapplicationparttypeprovider).

### Configuring job services

We should follow principals ASP.NET Core was build upon and adopt a plugin architecture in order to configure our pattern services. Using [a sample project](https://github.com/uveta/extensions-jobs/tree/main/samples/MvcDemo), I want to demonstrate how could this be achieved. The following example includes definition of one _endpoint_ and its bound _worker_.

<script src="https://gist.github.com/uveta/777c12716df3015ebc67a651916bea23.js"></script>

In this case, _PingRequest_ and _PingResponse_ correspond to input and output types, defined by user. _PingWorker_ represents a custom implementation of _IWorker_, which is also left to users. Other components (_endpoint_, _repository_ and _queue_) are provided by pattern and only configured here.

### Implementation alternatives

Proposed usage is just one of many possible. As all building blocks are introduced as abstract concepts, they can be adapted to different scenarios than initially intended. For example, an _endpoint_ could be utilized to issue an RPC call without knowing the address of remote service; client would use _endpoint_ to create a new job, which would reach designated _worker_ via queue, effectively removing the need of individual services knowing other ones even exist.

Further improvements can be introduced on _workers_ as well. In order to use them in a real-world scenarios, there has to be a possibility of configuring their maximum scaling and execution period limits. This is however very dependent on _queue_ implementation, as _worker_ is invoked based on actions triggered by _queue consumer_.

Finally, default memory implementations of _queue_ and _repository_ are only good for [demonstration purposes](https://github.com/uveta/extensions-jobs/tree/main/samples/MvcDemo). _Repository_ should offer some form of permanent external storage, especially if usage in multi-service applications is considered. Same goes for _queue_, as the need for underlying message broker is evident in even simple production scenarios.

## Conclusion

