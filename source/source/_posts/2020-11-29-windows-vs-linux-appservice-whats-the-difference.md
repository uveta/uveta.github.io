---
title: Windows vs Linux App Service - What is the Difference?
tags:
  - azure
  - advanced
categories:
  - blog
disqusId: windows-vs-linux-appservice-whats-the-difference
date: 2020-11-29 20:00:10
---


{% asset_img windows_linux.png Windows and Linux logo %}

I always wanted to devote an article to Azure App Service, as it is my personal weapon of choice for hosting Web Apps. And what better topic to choose, than the one I always found official documentation severely lacking: describing feature difference between Windows and Linux platforms.

<!-- more -->

## Why App Service?

To me, App Service offers perfect balance between ease of deployment and features supported. Adding to its maturity is a rich set of tools and plugins, promoting work in almost any environment.

A major decision, before even getting to deployment, is the choice of Windows or Linux platform. Without going deep into architectural details, I will try to explain main differences which should interest you as a consumer.

## Which App Service Plan to choose?

Platform on which App Service would be running is dictated by its App Service Plan. If underlying plan is running Windows, service will have to as well. Same goes for Linux. Hence, you cannot mix different platform services on a single plan.

Furthermore, due to the limitations of underlying networking implementation, plans with different platforms cannot even be a part of the same resource group. You will need at least 2 resource groups if you want to support both Windows and Linux deployments.

## Is my application stack supported?

App Service documentation states it can run applications written in following languages: .NET, .NET Core, Java, Node.js, Ruby, PHP and Python. However, depending on the platform, certain limitations are in place. Linux service is obviously unable to run older .NET applications, but only .NET Core, so Windows is the only alternative if using ASP.NET. On the other hand, Ruby is supported only on Linux service.

Good news is that both platforms support running containers, allowing further customization of execution environment. If runtime is not among supported languages, it will have to be included in the deployment image.

## Who handles incoming web requests?

Choice of platform also determines primary entry-point to the application. In case of Windows, Internet Information Services (IIS) is used, while Apache HTTP Server would be used on Linux. Since integration of IIS and App Service is far more mature, running under Linux imposes few limitations:

* Application will always run out of process, as opposed to in-process hosting. Even if using supported stack, i.e. .NET Core, it will still default to out of process hosting, under these circumstances. This can result in reduced throughput, especially if application is serving ample amounts of incoming requests.
* Custom path mappings are not available, forcing application hosting on root path only. There is, however, a possibility to provide up to 5 path mappings to external storage accounts, e.g. to serve static resources.
* Custom IIS features are clearly not supported. If IIS configuration file (web.config) is present, it will simply be ignored. This limits usage of IIS modules, such as application initialization.

## What do I do when things go wrong?

Linux users will also have hard time diagnosing running applications. For example, when running a Windows service, setting up remote debugging via Visual Studio (Code) is trivial, as it can be enabled by simply flipping a switch in App Service settings. Although it is possible to use remote debugging on Linux, via SSH tunnel, the setup is not straightforward and not available off the shelf, as it is the case with Windows.

Luckily, Linux App Service permits incoming SSH connection, which can be initiated from Azure Portal, as well as remotely, using preferred client. This opens a world of possibilities when trying to determine why running applications are not behaving optimally.

## Which App Service extensions can I use?

When talking about App Service extensions the situation is pretty clear: they are fully supported on Windows, and not supported at all on Linux. This can have implications if hosted application depends on any of them. For example, Azure Application Insights cannot be enabled from App Service, but application itself has to include Insights SDK. Instrumentation key can still be passed via App Service settings.

WebJobs are another victim in this case. They cannot be operated independently of the application, at least not in the way they are created and ran on Windows service. Although Linux offers some alternatives, such as cron jobs, they have to be setup and monitored manually via SSH.

## Any final words?

This article could have easily be named _"10 and more advantages of Windows over Linux App Service"_, but I decided to stick with my initial idea. It is obvious that Windows version is much more mature, offering a rich collection of additional features, while Linux is slowly catching up.

There are however some situations where you should choose the less mature option. Linux is currently your only choice if you want to run Ruby applications out-of-the-box, not including custom container option. In case cost optimization is important, Linux is generally a better choice, as it does not come with any operating system license charges.

I hope information in this article will make choosing your future App Service platform a bit more straightforward.
