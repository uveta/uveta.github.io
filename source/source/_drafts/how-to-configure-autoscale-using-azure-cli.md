---
title: how-to-configure-autoscale-using-azure-cli
tags:
---

az appservice plan create -g demo -n demo-plan --is-linux --location westeurope --sku S1

az monitor autoscale create -g demo -n demo-autoscale-settings --resource demo-plan --resource-type Microsoft.Web/serverfarms --min-count 1 --max-count 5 --count 1

az monitor autoscale rule create -g demo --autoscale-name demo-autoscale-settings --scale out 1 --condition "CpuPercentage > 75 avg 5m"

az monitor autoscale rule create -g demo --autoscale-name demo-autoscale-settings --scale in 1 --condition "CpuPercentage < 25 avg 5m"

az monitor autoscale delete -g demo -n demo-autoscale-settings

az appservice plan delete -g demo -n demo-plan

https://docs.microsoft.com/en-us/cli/azure/monitor/autoscale?view=azure-cli-latest
https://docs.microsoft.com/en-us/azure/azure-monitor/platform/autoscale-understanding-settings
https://docs.microsoft.com/en-us/azure/azure-monitor/platform/autoscale-common-metrics