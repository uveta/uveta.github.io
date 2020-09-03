---
title: Ahaaas - August 2020
tags:
  - azure
  - .net
  - csharp
  - containers
  - advanced
categories:
  - blog
disqusId: ahaaas-august-2020
---

{% asset_img cartoon-1294877.png Eureka %}

<!-- more -->

### .NET

* Instead of using `BlockingCollection`, recommended implementation of producer/consumer pattern for .NET Core should use [Channels](https://devblogs.microsoft.com/dotnet/an-introduction-to-system-threading-channels/). Besides granting full control of number of consumers, it is completely asynchronous and does not lead to thread blocking.

* If writing a custom ASP.NET Core middleware, be careful when injecting scoped services. Middlewares are registered as singletons, so scoped services cannot be injected via constructor <https://docs.microsoft.com/en-us/aspnet/core/fundamentals/middleware/write#middleware-dependencies>.

* There is no simple way to resolve `IHostedService` or `BackgroundService` from DI container. Event though hosted services should not be injected into other services, if you absolutely need to, you can do one of the following (ordered by recommendation, from "yes please" to "why would you do that"):
 * Do not implement `IHostedService` directly, but create a hosted wrapper which injects your service and activates it on start; inject service elsewhere and not the hosted wrapper.
 * Register service both as hosted service and a singleton; same instance will be resolved when injecting.
 * Inject `IEnumerable<IHostedService>` and find instance by concrete type.


* Use the following pattern if you require your event publisher to wait for asynchronous consumers. This can prove useful in case you are testing event driven units and require consumers to finish execution before code following publisher invocation continues. Another example is having application shutdown event, which could wait for consumers before exiting gracefully. Code was copied from [stackoverflow](https://stackoverflow.com/) but for the life of me I could not find the original answer, hence no direct link :(

```cs
public event Func<T, Task> ItemQueued;

public async Task OnNewItemAsync(T item)
{    
    if (ItemQueued is null) return;
    Func<T, Task> handler = ItemQueued;
    Delegate[] invocationList = handler.GetInvocationList();
    Task[] handlerTasks = new Task[invocationList.Length];
    for (int i = 0; i < invocationList.Length; i++)
    {
      handlerTasks[i] = ((Func<T, Task>)invocationList[i])(item);
    }
    await Task.WhenAll(handlerTasks);
}
```

* When developing server side Blazor application, HTML markup cannot be changed without having to rebuild the whole application. As this is something naturally supported by other SPA frameworks, you can achieve similar behaviour by running application with _dotnet watch run_. It will react to any change in source files, re-build and re-run application. It is not a perfect method in any way, but removes the need to manually stop, build and run every time.

* If you use Visual Studio you can have [Sass in Blazor](https://chrissainty.com/get-some-sass-into-your-blazor-app/)! It can be achieved by installing an extension and including a specific NuGet package to projects with _.scss_ files. In VS Code, you can use [Live Sass Compiler](https://github.com/ritwickdey/vscode-live-sass-compiler) extension.

* Having trouble finding .NET Core NuGet package storage and caches on your PC? Just use _dotnet nuget locals all --list_ to show all relevant paths. Documentation about additional commands for managing NuGet sources can be found [here](https://docs.microsoft.com/en-us/nuget/consume-packages/managing-the-global-packages-and-cache-folders).

* Selective testing using _dotnet test_ with _\-\-filter_ argument <https://docs.microsoft.com/en-us/dotnet/core/testing/selective-unit-tests?pivots=xunit>.

* Even though health checks were introduced in ASP.NET Core 2.2, there are certain issues with initial implementation, specifically when using `IHealthCheckPublisher`. Although these issues were solved in version 3.0, if you are still running 2.2 you might want to check [workarounds](https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/health-checks?view=aspnetcore-2.2#health-check-publisher-1).

### Azure

* Recommended option for blob storage https://docs.microsoft.com/en-us/azure/storage/blobs/soft-delete-enable
* Manually run Azure function Function App->Functions->Code + Test and click Test\Run button
* Elastic deployment to Azure - change disks
* MaxAutoRenewDuration should be larger than LockDuration

### Containers

* Online workshop showcasing Docker and Kubernetes for .NET developers https://dak4.net
* ACI Virtual Kubelet does not support health and readiness checks https://github.com/virtual-kubelet/azure-aci
* Not possible to turn off performance monitoring for AKS, only option is to disable monitoring alltogether https://docs.microsoft.com/en-us/azure/azure-monitor/insights/container-insights-optout

### Miscellaneous

* Ever wished you could develop in emojis? https://www.emojicode.org/
* https://www.howtogeek.com/117435/htg-explains-the-linux-directory-structure-explained/
* GitHub Container Registry https://github.blog/2020-09-01-introducing-github-container-registry/
