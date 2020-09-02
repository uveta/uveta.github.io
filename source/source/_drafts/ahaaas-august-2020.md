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
disqusId: ahaaas-july-2020
---

{% asset_img bulb.png Eureka %}

<!-- more -->

### .NET

### Azure

### Containers

### Miscellaneous

* Online workshop showcasing Docker and Kubernetes for .NET developers https://dak4.net
* "Recommended producer/consumer pattern should use Channels https://devblogs.microsoft.com/dotnet/an-introduction-to-system-threading-channels/"
* Ever wished you could develop in emojis? https://www.emojicode.org/
* Be careful of service scope injected into middleware https://docs.microsoft.com/en-us/aspnet/core/fundamentals/middleware/write
* Not possible to turn off performance monitoring for AKS, only option is to disable monitoring alltogether 
https://docs.microsoft.com/en-us/azure/azure-monitor/insights/container-insights-optout
* Recommended option for blob storage https://docs.microsoft.com/en-us/azure/storage/blobs/soft-delete-enable
* Manually run Azure function Function App->Functions->Code + Test and click Test\Run button
* Cannot resolve IHostedService or BackgroundService 1. write hosted wrapper that will "activate" the service and resolve service 2. register hosted service as hosted service and singleton 3. resolve IEnumerable<IHostedService> and find instance by type
* Asynchronous event pattern waiting for consumers

public event Func<T, Delivery, Task> ItemQueued;

public async Task OnNewItemAsync(T item)
        {
            var delivery = new Delivery(false);
            if (ItemQueued is null) return;
            Func<T, Delivery, Task> handler = ItemQueued;
            Delegate[] invocationList = handler.GetInvocationList();
            Task[] handlerTasks = new Task[invocationList.Length];
            for (int i = 0; i < invocationList.Length; i++)
            {
                handlerTasks[i] = ((Func<T, Delivery, Task>)invocationList[i])(item, delivery);
            }
            await Task.WhenAll(handlerTasks);
        }
* Elastic deployment to Azure - change disks
* When developing server side Blazor application, HTML markup cannot be changed without having to rebuild the whole application. As this is something that is naturally supported by JavaScript, you can achieve similar behaviour by running application with dotnet watch run
* MaxAutoRenewDuration should be larger than LockDuration
* dotnet nuget locals all --list https://docs.microsoft.com/en-us/nuget/consume-packages/managing-the-global-packages-and-cache-folders
* Selective testing using dotnet test https://docs.microsoft.com/en-us/dotnet/core/testing/selective-unit-tests?pivots=xunit
https://www.howtogeek.com/117435/htg-explains-the-linux-directory-structure-explained/
* Using SASS in Blazor application (via VS extension and NuGet) https://chrissainty.com/get-some-sass-into-your-blazor-app/
* ACI Virtual Kubelet does not support health and readiness checks https://github.com/virtual-kubelet/azure-aci
* asp net core 2.2 health check issues https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/health-checks?view=aspnetcore-2.2#health-check-publisher-1
* GitHub Container Registry
