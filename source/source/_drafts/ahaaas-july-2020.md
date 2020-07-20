---
title: Ahaaas - July 2020
tags:
  - azure
  - .net
  - csharp
  - advanced
categories:
  - blog
disqusId: ahaaas-july-2020
---

Developer ahaaas

<!-- more -->

* License overview <https://choosealicense.com>
* How to mock authenticcation in ASP.NET Core 3.1 integration tests <https://docs.microsoft.com/en-us/aspnet/core/test/integration-tests?view=aspnetcore-3.1#mock-authentication>
* Even though ASP.NET Core configuration entries are case-insensitive, with IConfiguration.Bind property name case does matter. If you can, use IServiceCollection.Configure extension for binding configuration to types.
* Someone used Blazor to create original Pacman <http://pacmanblazor.azurewebsites.net>
* ASP.NET Core rate limiting <https://github.com/stefanprodan/AspNetCoreRateLimit>
* Input model validation using ApiController and DataAnnotations <https://docs.microsoft.com/en-us/aspnet/core/mvc/models/validation>
* Be careful when using arrays in appsettings.json, especially if you consider overriding values in environment specific configurations <https://rimdev.io/avoiding-aspnet-core-configuration-pitfalls-with-array-values/>
* If you have a type implementing two interfaces and register each one as singleton in DI, you will end up with different instances for each interface resolved
* If you use sessioning in Azure service bus, make sure your enqueued message has session id assigned. Service Bus will not complain on enqueue, but will make hell for consumers on delivery and practically keep message in queue until expired.
* You can use Azure Log Analytics to collect custom VM logs <https://docs.microsoft.com/en-us/azure/azure-monitor/platform/data-sources-custom-logs>. In Portal, open your workspace resource and go to Advanced Settings->Data->Custom Logs. You can also collect Windows Event and IIS Logs, as well as Linux Syslog, in a similar fashion.
* Data sampling, configured in Application Insights usage and estimated costs, has no effect if you are using ASP.NET core without modification to telemetry sampling configuration. Use Daily Cap setting to get notified if data ingested ammount passes the limit. Can increase costs, especially if Azure Functions are used.
* If you are looking for Blazor components that you can use for commercial projects, Syncfusion offers a bunch of them via their community license <https://www.syncfusion.com/products/communitylicense>
* Cool portability tool from .NET to .NET core comes from, you wouldn’t believe it, AWS team. More details <https://aws.amazon.com/blogs/aws/announcing-the-porting-assistant-for-net/>
* System.Net.Mail.SmtpClient is obsolete for some time, recommended alternative <https://github.com/jstedfast/MailKit>
* Postman automatically converts POST to GET requests if it receives a 301 redirect status code. In this situation, it also drops any request content and queries redirecting location without body data. This can lead to confusion as user is never notified of such change during request execution. To avoid such issues, better turn off automatic redirects in Postman settings.
* Microsoft Q&A is generally available and should help you find any technical information regarding Azure services <https://docs.microsoft.com/en-us/answers/products/>
* _IApplicationLifetime_ service is obsolete from .Net Core 3.0. If using generic host, inject _IHostApplicationLifetime_ instead <https://docs.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime>
