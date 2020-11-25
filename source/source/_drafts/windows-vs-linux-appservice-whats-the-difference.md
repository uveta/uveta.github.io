---
title: Windows vs Linux App Service - What is the Difference?
tags:
  - azure
  - advanced
categories:
  - blog
disqusId: windows-vs-linux-appservice-whats-the-difference
---

{% asset_img windows_linux.png Windows and Linux logo %}

As Azure App Service is my personal weapon of choice, when hosting Web Apps, I always wanted to devote it at least one article. And what better topic to choose, than the one I always found official documentation severely lacking: describing exactly which features are available on each App Service platform.

<!-- more -->

## Why should you use App Service

App Service offers perfect balance between ease of deployment, supported features and additional tools available.

## Language stacks

App Service documentation states it can run applications written in following languages: .NET, .NET Core, Java, Node.js, Ruby, PHP and Python. However, depending on platform it is running on, certain limitations are in place. Linux service can obviously not run older .NET applications, but only .NET Core, so Windows is the only alternative if using .NET framework. On the other hand, Ruby is not supported on Windows service.

Both service types allow running applications from containers. If runtime is not among supported languages, it will have to be included in the deployed image.

## Resources

Operating system on which App Service is running is determined by its' App Service Plan. Hence, you cannot mix service with different operating system on a single plan. It is not allowed to create Linux service on Windows plan and vice versa. 

Furthermore, due to the limitations of underlying networking implementation, plans with different OS cannot be a part of the same resource group. You will need at least 2 resource groups if you want to support both Windows and Linux deployments.

## Web server limitations

Choice of OS also determines primary entry-point to the application. In case of Windows, Internet Information Services (IIS) is used, while Apache HTTP Server would be used on Linux. Since integration of IIS into App Service is far more mature, running under Linux imposes some limitations:

* Application will always run out of process, as opposed to in-process hosting. Even if using supported stack, such as .NET Core, it will still default to out of process hosting under these circumstances. This can result in reduced throughput, especially if application in serving ample amounts of incoming requests. 
* Custom path mappings are not available, so application will only be hosted on root path. There is, however, a possibility to provide up to 5 path mappings to external storage accounts, e.g. to serve static resources.
* Custom IIS features are obviously not supported. If IIS configuration file (web.config) is present, it will simply be ignored. This limits usage of IIS modules, such as application initialization.

## Diagnostics

Linux users will also have hard time trying to diagnose running applications. For example, when running a Windows service, setting up remote debugging via Visual Studio is trivial, as it is literally an enable/disable switch in App Service settings. Although it is possible to use remote debugging on Linux, via SSH tunnel, the setup is not so straightforward and not available off the shelf, as it is the case with Windows.

Luckily, App Service allows setting up SSH connection, which can be established remotely, using preferred client, as well as from Azure Portal. This opens a world of possibilities when trying to determine why running applications are not behaving optimally.

## Extensions

When talking about App Service extensions the situation is pretty clear: they are fully supported on Windows, and not supported at all on Linux. This can have implications if application depends on some of them. For example, Azure Application Insights cannot be enabled from App Service, but application itself has to enable integration via appropriate SDK. Instrumentation key can still be passed via App Service settings.

WebJobs are another victim in this case. They cannot be operated independently of the application, at least not in the way they created and ran on Windows service. Although Linux offers some alternatives, such as cron jobs, they have to be setup and monitored manually via SSH.

## Conclusion

This article could have easily be named "10 advantages of Windows over Linux App Service", but I decided to stick with my initial idea. It is obvious that Windows version is much more mature, offering a rich collection of additional features, while Linux is slowly catching up.

There are however some situations where you should choose the less mature option. Linux is currently your only choice if you want to run Ruby applications out-of-the-box. In case cost optimization is important, Linux is generally a better choice, as it does not come with any operating system license charges.
