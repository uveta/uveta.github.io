---
title: Azure Functions Deep Dive
tags:
  - azure
  - advanced
categories:
  - blog
disqusId: azure-functions-deep-dive
---

{% asset_img banner.jpg Function in action %}

Ever wondered what happens under the hood of Azure Functions? How can your server code magically run without a server? How does it know when to scale and how much? How to adjust your application to run optimally as a function? I've got you covered.

<!-- more -->

## How did it all start

In 2014 (ancient history!) Microsoft introduced WebJobs. As a part of their Azure Web Sites PAAS offering (later re-branded as Azure Web Apps), it allowed uses to run arbitrary application in Azure, be it a Windows executable, Powershell, bash script, or code written in .NET, Java, PHP, Python or JavaScript. Not only that, but it had built-in input and output bindings for various Azure resources, such as blob storage, queue or Service Bus. From the get-go, you could trigger your WebJob from all of these resources, and write to them, without having to handle communication yourself. Later extensions allowed jobs to be triggered by CRON timers and HTTP requests. Sounds familiar? This is how Azure WebJobs SDK came to be, which later served as a base platform for Azure Functions.

Although they are still available as part of Web Apps (check [Web Jobs](https://docs.microsoft.com/en-us/azure/app-service/webjobs-create) blade on your App Service) and have their fair share of usage, Web Jobs have mostly been replaced with higher order resources, such as Function or Logic Apps.

## Components

As we established in the previous section, Azure WebJobs SDK is an essential piece of Function Apps. It is responsible for running custom application and provides bindings to external resources. Another part is, for completeness' sake, the custom application itself, serving whatever purpose user sees fit. But we mustn't forget about another, equally important component. The glue that ties it all together, the [Azure Functions Host](https://github.com/Azure/azure-functions-host).

### Functions Host

Functions Host is, in its core, a lightweight ASP.NET Core application running on top of Azure WebJobs SDK. It serves as a connector between custom application (executed by a trigger), all _function.json_ files (containing individual functions metadata) and single _host.json_ file (host configuration including logging, specific trigger options and various extension settings, common for all functions that are part of a single Function App).

Custom application itself is discovered and managed by Functions Host, and executed via WebJobs SDK. Scaling, however, lies outside of responsibilities of Host instances. It is managed by an independent component - Scale Controller.

### Scale Controller

Just as Functions Host is responsible for handling and running individual functions, Scale Controller also plays a managerial role, but for Host instances themselves. It is in charge of creating and destroying individual Hosts, depending on the current workload. Scaling decisions are made based on metrics gathered from resources used as input triggers. Scale Controller is responsible for consuming these metrics and adjusting number of active Host instances.

As an example, let us have a function triggering of an Azure Service Bus. Scale Controller would have to monitor current number of active messages, as well as maximum message throughput obtained from configuration. In case active message count starts rapidly rising, it will spin up new instances to handle increased load. As number of messages decreases, so will inactive instances be destroyed by Scale Controller.

## Deployment and Hosting

I have already mentioned that Function Apps support plethora of programming languages and scripts, e.g .NET, Java, JavaScript, Powershell and Bash, and the list goes on. Even containers are supported, as custom Docker images can be hosted in Function Apps as well.

On the other hand, multiple hosting plan scenarios are possible: Consumption, running functions only when required; Dedicated, using deployed App Service Plans to run functions alongside existing App Services; and finally Premium, using dedicated elastic App Service Plans to offer unparalleled performance, response time and scaling capabilities. Each of them has its strengths and weaknesses, and I encourage you to go through official documentation before considering function usage.

Overabundance of supported application and hosting models sadly has its negative sides - not all combinations are supported and not all of them operate effectively together. When other factors are taken into account, such as in-process and out-of process hosting, supported runtimes and operating systems, as well as minimum performance requirements, available options can seriously get limited. I would recommend reading comparison of [Azure Functions hosting plans](https://docs.microsoft.com/en-us/azure/azure-functions/functions-scale) before getting seriously involved. The article also contains well-organized support tables for each of hosting options.

## Tips and trick

I could have named this section 'Azure Functions Best Practices', but Microsoft already [beat me to it!](https://docs.microsoft.com/en-us/azure/azure-functions/functions-best-practices) Instead of repeating the same conventions you've heard over and over again, I wanted to focus on something different. Specifically, on certain points that I became aware through my experience working with functions. Information following will help you better understand what and what functions can't be used for, how to use them in a more efficient way and what to do if it all goes haywire.

### Logging

Unless you added your own custom logging infrastructure, there is a good chance that all telemetry, requests and traces are consumed by Application Insights associated with your Function App. I will not go into detail on how to query this data, but will leave you with a [handy explanation](https://docs.microsoft.com/en-us/azure/azure-functions/configure-monitoring) on how it is organized and how to locate individual function components logs.

Default logging configuration is pretty great, and you would not need to modify it 99.99% of the time. But there is still that 00.01% that will eventually come up, be it during development or trying to solve an issue in production. As all .NET Core applications, functions use structured logging as well, which can be adjusted either by modifying _host.json_ configuration directly, or via [application settings](https://docs.microsoft.com/en-us/azure/azure-functions/functions-host-json#override-hostjson-values). The most interesting categories that support modifying logging levels are:

* Function - includes execution start/stop time, dependencies tracking, thrown exceptions, as well as any logs from custom application, in case the code is consuming provided ILogger instance
* Host - contains function invocation count, success rate and duration telemetries; invocation success and failures are also logged
* Microsoft - in case custom application was written in .NET, handles logs coming from .NET components used, e.g. HttpClient, Hosting etc.
* Worker - if running non .NET application, its' console output would be written using this category

Reading the list, you must have thought: "Cool, so I can monitor individual function components. But wait a minute... Where is the Scale Controller?!". No worries, Microsoft is on the case. Gathering logs from [Scale Controller](https://docs.microsoft.com/en-us/azure/azure-functions/functions-monitoring?tabs=cmd#scale-controller-logs) is available, but still as a preview feature. For now, it can be configured exclusively using application settings.

### Function sandbox

It goes without saying, but don't expect your functions to be running on bare metal. Whats more, they are not even hosted on dedicated Virtual Machine. Instead, Function Apps are constrained to what is called an [Azure Web App sandbox](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox), which, by the way, is the same environment running App Services. The sandbox imposes many limitations, but most important ones, from the point of view of Function App developer, are:

* no access to Windows shared components - meaning no writing to registry, no service management using SCM and no access to processes outside of sandbox. One more important feature missing is usage of GDI32 graphics subsystem, which is still, sadly, used by most PDF generator tools.
* no disk access - generally, you are not able to read/write to disk even if the target path exists. There are however two exceptions: you can either use storage shared by all sandboxes (/home) or local sandbox storage (/local). Note, however, that local storage is deleted when host instance is terminated.
* limited network features - custom application is allowed to listen on any incoming port, but that port will only be available within the sandbox and not to external clients. It cannot use localhost or 127.0.0.1 address to open connection to ports it did not open itself. On the other side, some of the outgoing ports are restricted, namely 137, 138, 139 and 445.

### Function performance counter limits

These limitations are essentially imposed by the Web App sandbox environment as well. Unlike the ones listed in the previous chapter, these ones can be circumvented if your application is properly adapted to run in Function App. Please note that they apply only when hosting on Consumption Plan; if you are hosting using Dedicated or Premium Plans, limitations will depend on VM size used. All the limitations are tracked by [Host Health Monitor](https://github.com/Azure/azure-functions-host/wiki/Host-Health-Monitor), which will start issuing warnings once function approaches or breaks any of the limits. Keep an eye out on Application Insights, where all function failures are logged.

#### Number of active (600) and total (1200) connections

If you are developing a function requiring a lot of external resources, you can easily find yourself exhausting all available active connections. As each function can be ran multiple times (e.g. when it is triggered by a queue), as part of the same sandbox, the limit of 600 connections applies to all the instances combined.

* Best trick to avoid breaking the limit is having a single HttpClient instance and reusing it. It is thread safe and will spare you the need to constantly create and dispose of HttpClients, which is a bad practice overall. 
* If multiple functions can reuse same external data, consider caching it in shared storage. 
* Try to do as little work in a function as possible, as long running functions tend to exhaust connection limit more often.

#### Number of threads (512)

If you are using System.Threading types to start threads manually, consider switching to Tasks, as they are able to utilize thread pool more efficiently. As a general advice, do not try to run many things in parallel, so avoid Threads, TPL and similar multithreaded libraries. It is better to have multiple smaller functions, than trying to handle everything in a single one.

#### Number of child processes (32)

Consider this limitation if your function has to spawn other processes. Too many child processes can also lead to increased memory usage, which directly affects billed costs of your Function App. In case Consumption Plan is not used, excessive process spawning can lead to underlying VM memory exhaustion.

### Override host configuration in code

If you notice parts of custom _host.json_ configuration are ignored by FunctionApp, using default values instead, you might be running into the same issue I stumbled upon couple of times. Yet there is a way to ensure overriding host configuration, if you are running in-process application. [The solution](https://github.com/Azure/azure-functions-servicebus-extension/issues/81#issuecomment-621431750]) requires injecting dependencies using _FunctionStartup_, and is especially useful for changing extensions configuration.

## Conclusion

Deep dive into Azure Functions originally started as another [Ahaaas article](/categories/blog/ahaaas-august-2020/). As I have noticed there are more than five different points concerning functions, I decided to dig a bit deeper and bring you this text.

In this article, I brought up ancient Azure history, in order to explain how Azure Functions came to be. Furthermore, I dissected most important function components and went through deploying and hosting options. Finally, I have included some of my personal experiences working with Azure Functions.
