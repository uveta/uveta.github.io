---
title: azure-functions-deep-dive
tags:
---

built on top of Azure WebJobs SDK - but doesnt require Azure https://github.com/Azure/azure-functions-host
azure functions host
in process and out of process hosting https://weblog.west-wind.com/posts/2019/Mar/16/ASPNET-Core-Hosting-on-IIS-with-ASPNET-Core-22
scaling model https://docs.microsoft.com/en-us/azure/azure-functions/functions-scale#how-the-consumption-and-premium-plans-work
	no container on consumption plan
Pay attention to Azure function limits https://github.com/Azure/azure-functions-host/wiki/Host-Health-Monitor
Overview of Azure Web App sandbox, used by Azure Functions and Azure App Services https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox
Override host.json values by app settings https://docs.microsoft.com/en-us/azure/azure-functions/functions-host-json#override-hostjson-values
If using function startup, extension options defined in host.json could be ignored https://github.com/Azure/azure-functions-servicebus-extension/issues/81#issuecomment-621431750
