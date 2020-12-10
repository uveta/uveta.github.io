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

created from the need to run long running operations using RESTful operations [the pattern](http://restalk-patterns.org/long-running-operation-polling.html). But it could be used in many other ways, as it generalizes the process of submitting requests. With some consideration, the pattern can be extended to provide a scalable solution needed by micro-service architectures.  

## Building blocks

### Job

The star of the show, based on which the whole pattern was named. Job represents a client intent that has to be processed by a background worker, and should include following information:

* header, containing job metadata such as unique identifier, current status, time of creation/start, potential issues as well as estimated time of completion
* input parameters
* output values

### Worker

Component that will do the heavy lifting. Main responsibility is actual processing of individual jobs, as each worker is able to handle jobs of certain type. In simple terms, we can describe it as a function accepting input parameters and returning either job output values or an error.

### Endpoint

Serves as an entry-point for clients. After creating a job, client should be able to obtain job status at any point in time, as well as output after processing is finished. Hence, endpoints need to provide following services:

* create job
* get job status
* get job output
* delete job
* delete job output

### Job Repository



### Job Queue



## ASP.NET Core

### Implementation considerations
input and output as strings

### Worker invoker

### Jobs Controller

## Conclusion