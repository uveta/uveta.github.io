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

In 2014 (ancient history!) Microsoft introduced WebJobs. As a part of their Azure Web Sites PAAS offering (later re-branded as Azure Web Apps), it allowed uses to run arbitrary application in Azure, be it a Windows executable, Powershell or bash script, or code written in .NET, Java, PHP, Python and JavaScript. Not only that, but it had built-in input and output bindings for various Azure resources, such as blob storage, queue or Service Bus. From the get-go, you could trigger your WebJob from all of these resources, and write to them, without having to handle communication yourself. Later extensions allowed jobs to be triggered by CRON timer and HTTP request. Sounds familiar? This is exactly the feature that gave birth to Azure WebJobs SDK.

Although they are still available as part of Web Apps (check [Web Jobs blade](https://docs.microsoft.com/en-us/azure/app-service/webjobs-create) on your App Service) and have their fair share of usage, Web Jobs have mostly been replaced with higher order resources, such as Function or Logic Apps. 

## Components

As we established in the previous section, Azure WebJobs SDK is an essential part of Function Apps. It is responsible for running custom application and provides bindings to external resources. Another part is, for completeness' sake, the custom application itself, responsible for whatever purpose user sees it fit. But we mustn't forget about another, equally important component. The glue that ties it all together, the [Azure Functions Host](https://github.com/Azure/azure-functions-host).

## Function Host


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
