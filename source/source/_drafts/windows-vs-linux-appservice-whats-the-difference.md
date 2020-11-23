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

personal weapon of choice when it comes to hosting Web Apps

<!-- more -->

## Language stacks

App Service documentation states it can run applications written in following languages: .NET, .NET Core, Java, Node.js, Ruby, PHP and Python. However, depending on OS plan is running on, certain limitations are in place. Linux services can obviously not run older .NET applications, but only .NET Core, so Windows is the only alternative if using legacy framework. On the other hand, Ruby is not supported on Windows services.

Both service types allow running applications from containers. If runtime is not among supported languages, it will have to be included in the deployed image.

## Resources

Operating system on which App Services is running is determined by its' App Service Plan. It is not allowed to create Linux services on Windows plans and vice versa. Which means you cannot mix them on a single plan.

Furthermore, due to the limitations of underlying networking implementation, plans with different OS cannot be a part of the same resource group. Meaning you will require at least 2 resource groups if you need to support both Windows and Linux deployments.

## Diagnostics

limited app service diagnostic
no remote debugging on linux
no process explorer on linux

## Web server limitations

Choice of OS also determines primary entry-point to the application. In case of Windows, Internet Information Services (IIS) is used, while Apache HTTP Server would be used on Linux. Since integration of IIS into App Service is far more mature, running under Linux imposes some limitations:

* Application will always run out of process, as opposed to in-process hosting. Even if using supported stack, such as .NET Core, it will still default to out of process hosting under these circumstances. This can result in reduced throughput, especially if application in serving ample amounts of incoming requests. 
* Custom path mappings are not available, so application will only be hosted on root path. There is, however, a possibility to provide up to 5 path mappings to external storage accounts, e.g. to serve static resources.
* Custom IIS features are obviously not supported. If IIS configuration file (web.config) is present, it will simply be ignored. This limits usage of IIS modules, such as application initialization.

## No extension support

no extensions on linux
webjobs not supported on linux
application insights as extension cannot be used on linux; SDK is needed

## Conclusion

This article could have easily be called "10 advantages of Windows over Linux App Services", but I decided to stick to my initial idea. It is obvious that Windows version is much more mature, offering a rich collection of additional features, while Linux is slowly catching up.

There are however some situations where you should choose the less mature option. Linux is currently your only choice if you want to run Ruby applications out-of-the-box. In case cost optimization is important, Linux is generally a better choice, as it does not come with any operating system license charges.
