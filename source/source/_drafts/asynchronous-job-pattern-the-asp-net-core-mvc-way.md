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

_This post is part of [C# Advent Calendar 2020](https://www.csadvent.christmas/). Cheers to [Matt Groves](https://twitter.com/mgroves) for letting me participate!_

created from the need to run long running operations using RESTful operations [the pattern](http://restalk-patterns.org/long-running-operation-polling.html). But it could be used in many other ways, as it generalizes the process of submitting requests. With some consideration, the pattern can be extended to provide a scalable solution needed by micro-service architectures.  

## Building blocks

### Job

The star of the show, based on which the whole pattern was named. Job represents a client intent that has to be processed by a background worker, and should include following information:

* header, containing job metadata such as unique identifier, current status, time of creation/start, potential issues as well as estimated time of completion
* input parameters
* output values

### Worker

Component that will do the heavy lifting. Main responsibility is actual processing of individual jobs, as each _worker_ is able to handle jobs of certain type. In simple terms, we can describe it as a function accepting input parameters and returning either job output values or an error.

### Endpoint

Serves as an entry-point for clients. After creating a job, client should be able to obtain job status at any point in time, as well as output after processing is finished. It should also allow client to cancel and clean-up a job. Hence, _endpoint_ needs to provide following services:

* create job
* get job status
* get job output
* delete job
* delete job output

### Job Repository and Queue

We will save all jobs in a _repository_, which will allow individual pattern components to read and update their state. For now, let's consider a repository an abstraction, as concrete implementation and storage technology may vary, depending on usage scenario.

In order to establish a binding between an _endpoint_ and a _worker_, let's introduce a concept of a _job queue_. Similar to how _repository_ was defined, implementation may range from memory one to a hyper-scale message broker. But all of them will have one thing in common: they have to provide both a _producer_ and a _consumer_, depending on which component requires _queue_ services.

Final pattern architecture is depicted in the diagram bellow. As we have all core components defined, why don't we move on to more amusing part and see how could we implement it using APS.NET Core.

![Job pattern architecture](architecture.png)

## ASP.NET Core

Final version can be found on [Github](https://github.com/uveta/extensions-jobs).

### Implementation considerations

In order to make the implementation as universal as possible, we need to limit out inputs and outputs to one of each. If multiple values are expected, they could be provided via a custom model. _JobExecutionResult_ will serve as a wrapper for output value, detailing result of _worker_ execution and any possible issues. If we define them as types _TInput_ and _TOutput_, our _worker_ and _endpoint_ could look something like this:

<script src="https://gist.github.com/uveta/85943b7354871239058c4b45ffca8ee9.js"></script>
<script src="https://gist.github.com/uveta/9ebe4d6514c8bf6d22dca908eeeb0c04.js"></script>

As _endpoint_ is user independent concept, it will be up to pattern to provide the implementation. On the other hand, _worker_ is responsible for executing custom actions, hence it's implementation has to be provided by the patter user. This reasoning will come into play during design, as we would like to provide plugin architecture for individual pattern components. This will allow utilizing only building blocks required for given scenario, while the user would provide implementation of parts requiring customization.

### Plugging in worker implementation

The role of a _worker_ is simple: it should listen for incoming jobs from _queue_; whenever one is received, it should process it and update job's status and output using _repository_; then it waits for the next one... This cycle is supposed to run for the whole application lifetime, as new jobs can arrive at any time. A natural solution to implement such functionality are [hosted services](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/host/hosted-services). In our case, we could extract all common code (starting queue consumer, input/output serialization and job update) to a _WorkerInvoker&lt;TWorker&gt;_ hosted service, bound to specific _worker_ by it generic type _TWorker_.

Adding _TWorker_ type to DI container has multiple benefits. On one side, it can be injected into _WorkerInvoker&lt;TWorker&gt;_, removing the need to construct it manually. On the other hand, it allows developers to freely inject business services into it.

### Exposing endpoints

_Endpoint_ represents an entry point for pattern clients, which is the responsibility of controllers in MVC world. In our case, we should use a controller. Its methods and client behavior should follow [pattern definition](http://restalk-patterns.org/long-running-operation-polling.html) to the letter:

* Allow creating new jobs using POST requests; respond with status 202 (Accepted), containing job resource URL, where its status can be queried
* Client should poll for job status using URL from previous step
* When job processing is finished successfully, use 303 (See Other) redirect to provide client with an output resource URL
* In case of job failure or cancellation, it is up to client to stop polling
* After polling finishes, client should issue DELETE request to job resource URL in order to dispose of reserved resources; if not done by client, server should clean up old jobs

The burden of creating such controller should not fall on the user; it is the responsibility of the pattern itself, as flow can be generalized. For this purpose we should implement a generic _JobsController&lt;TEndpoint&gt; which would be bound to specific _endpoint_ via its type parameter _TEndpoint_. 

### Configuring job services

As ASP.NET Core is used as our primary hosting environment, we should clearly follow principals it was build upon and use a plugin architecture for configuring services. Using [one of the sample projects](https://github.com/uveta/extensions-jobs/tree/main/samples/MvcDemo), I want to demonstrate what kind of solution should we strive for. The following example includes definition of one _endpoint_ and its bound _worker_.

<script src="https://gist.github.com/uveta/777c12716df3015ebc67a651916bea23.js"></script>

In this case, _PingRequest_ and _PingResponse_ correspond to input and output types, defined by user. _PingWorker_ represents a custom implementation of _IWorker_, which is also left to users. Other components (_endpoint_, _repository_ and _queue_) are provided by pattern and only configured here.

### Implementation alternatives

use endpoints in different scenarios
other queue and repository implementations
scaling and limiting workers

## Conclusion