---
title: Azure Functions Deep Dive
tags:
  - azure
  - advanced
categories:
  - blog
disqusId: azure-functions-deep-dive
---

![Placeholder](https://via.placeholder.com/800x350/0000FF/808080/?text=Banner)

Ever wondered what happens under the hood of Azure Functions? How can your server code magically run without a server? How does it know when to scale and how much? How to adjust your application to run optimally as a function? I've got you covered.

<!-- more -->

## How did it all start

In 2014 (ancient history!) Microsoft introduced WebJobs. As a part of their Azure Web Sites PAAS offering (later re-branded as Azure Web Apps), it allowed uses to run arbitrary application in Azure, be it a Windows executable, Powershell, bash script, or code written in .NET, Java, PHP, Python or JavaScript. Not only that, but it had built-in input and output bindings for various Azure resources, such as blob storage, queue or Service Bus. From the get-go, you could trigger your WebJob from all of these resources, and write to them, without having to handle communication yourself. Later extensions allowed jobs to be triggered by CRON timer and HTTP request. Sounds familiar? This is how Azure WebJobs SDK came to be, which later served as platform on which Azure Functions are based on.

Although they are still available as part of Web Apps (check [Web Jobs](https://docs.microsoft.com/en-us/azure/app-service/webjobs-create) blade on your App Service) and have their fair share of usage, Web Jobs have mostly been replaced with higher order resources, such as Function or Logic Apps. 

## Components

As we established in the previous section, Azure WebJobs SDK is an essential piece of Function Apps. It is responsible for running custom application and provides bindings to external resources. Another part is, for completeness' sake, the custom application itself, responsible for whatever purpose user sees it fit. But we mustn't forget about another, equally important component. The glue that ties it all together, the [Azure Functions Host](https://github.com/Azure/azure-functions-host).

### Functions Host

Functions Host is, in its core, a lightweight ASP.NET Core application running on top of Azure WebJobs SDK. It serves as a connector between custom application, executed by a trigger, all _function.json_ files, containing individual functions metadata, and single _host.json_ file, containing host configuration including logging, specific trigger options and various extension settings, common for all functions that are part of a single Function App.

Custom application itself is discovered and managed by Function Host, and executed via WebJobs SDK. Scaling, however, lies outside of responsibilities of Host instances. It is managed by an independent component - Scale Controller.

### Scale Controller

Just as Functions Host is responsible for handling and running individual functions, Scale Controller also plays a managerial role, but for Host instances themselves. It is in charge of creating and destroying individual Hosts, depending on the current workload. Scaling decisions are made based on metrics coming from resources used as input triggers. Scale Controller is responsible for consuming these metrics and work out optimal number of Host instances. 

As an example, let us have a function triggering of an Azure Service Bus. Scale Controller would have to monitor current number of active messages, as well as maximum message throughput obtained from configuration. In case active message count starts rapidly rising, it will spin up new instances to handle increased load. As number decreases, so will Scale Controller start destroying inactive instances.

## Deployment and Hosting

I have already mentioned that Function Apps support plethora of programming languages and scripts, e.g .NET, Java, JavaScript, Powershell and Bash, just to name a few. Heck, even containers could be involved, as Docker images can be hosted in Function Apps as well. 

On the other hand, multiple hosting scenarios are possible: consumption, running functions only when required; dedicated, that uses existing App Service Plans to run functions beside existing App Services; and finally premium, using dedicated elastic App Service Plans to offer unparalleled performance, response time and scaling capabilities. Each of them has its strengths and weaknesses, and I encourage you to go through official documentation before deciding to use functions.

Overabundance of supported application and hosting models sadly has its negative sides - not all combinations are supported and not all of them operate effectively together. When other factors are taken into account, such as in-process and out-of process hosting, supported runtimes and operating systems, as well as minimum performance requirements, available options can seriously get limited. I would recommend reading comparison of [Azure Functions hosting plans](https://docs.microsoft.com/en-us/azure/azure-functions/functions-scale) before getting seriously involved. The article also contains well-organized support tables for each of hosting options.



## Tips and trick

I could have named this section Azure Functions Best Practices, but Microsoft already [beat me to it](https://docs.microsoft.com/en-us/azure/azure-functions/functions-best-practices). Instead of repeating the same points, I wanted to focus on additional points that I became aware through my experience working with functions. Information following will not only help you better understand what and what can't functions be used for, but also how to use them more efficiently and what to do in case you encounter or suspect any issues.


built on top of Azure WebJobs SDK - but doesnt require Azure https://github.com/Azure/azure-functions-host
azure functions host
in process and out of process hosting https://weblog.west-wind.com/posts/2019/Mar/16/ASPNET-Core-Hosting-on-IIS-with-ASPNET-Core-22
scaling model https://docs.microsoft.com/en-us/azure/azure-functions/functions-scale#how-the-consumption-and-premium-plans-work
	no container on consumption plan
logging categories and levels https://docs.microsoft.com/en-us/azure/azure-functions/functions-monitoring?tabs=cmd#configure-categories-and-log-levels
Pay attention to Azure function limits https://github.com/Azure/azure-functions-host/wiki/Host-Health-Monitor
Overview of Azure Web App sandbox, used by Azure Functions and Azure App Services https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox
Override host.json values by app settings https://docs.microsoft.com/en-us/azure/azure-functions/functions-host-json#override-hostjson-values
If using function startup, extension options defined in host.json could be ignored https://github.com/Azure/azure-functions-servicebus-extension/issues/81#issuecomment-621431750

## Conclusion
