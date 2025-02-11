---
title: Deploy and use DeepSeek R1 with Azure and .NET
date: 2025-02-09 13:31:06
tags:
  - advanced
  - dotnet
  - csharp
  - azure
  - ai
categories:
  - blog
disqusId: unity-in-21-days
---

{% asset_img deepseek_azure.png DeepSeek Azure %}

DeepSeek models have surely taken technological and finacial world by storm, showing that state-of-the-art AI development is not limited to the specific valley made out of sillicone, but has become a global affair. For people focused on Microsoft technologies, which has traditionally partnered with OpenAI, there are actually some great news. Azure cloud platform already supports DeepSeek R1 model, via it's Azure AI Foundry service. Let's take a look how to deploy such model, and how to consume it from .NET applications.

<!-- more -->

## Deploy

Getting DeepSeek model up and running on Azure is actually quite simple, even if you never used Azure AI Foundry (formerly known as Azure AI Studio). First, you need to create a new hub and project, which act as a container for your AI applications and models. This is done via AI Foundry [Management center](https://ai.azure.com/managementCenter/allResources). Pay attention to the region you are creating hub and project in, as this will affect model availability. As of February 2025, DeepSeek R1 is only available in East US, East US 2, West US, West US 3, South Central US, and North Central US regions.

![Creating Azure AI Foundry hub](create_hub.png)

![Creating Azure AI Foundry project](create_project.png)

Once you have your hub and project created, you can deploy DeepSeek R1 model. This is done via the [Model catalog](https://ai.azure.com/explore/models) tab in your project. Search for the "DeepSeek R1" model, and then click on "Deploy" button. You will be asked to provide a globally unique name for your model, and to optionally use content filters. Once you click "Deploy", Azure will start deploying the model, which can take a few minutes.

![Deploying DeepSeek R1 model](deploy_model.png)

Once the model is deployed, you will find it in your project's [Models + endpoints](https://ai.azure.com/build/deployments/model) tab. Select the deployment to get more information about it, including the endpoint URL and API key, which you will use to consume the model programmatically.

![DeepSeek R1 deployment info](deployment_info.png)

<!-- Playground -->
To make sure deployment is working as expected, you can use chat playground, which is availble in the [Playgrounds](https://ai.azure.com/playgrounds) tab of your project. Select the DeepSeek R1 deployment, and then you can start chatting with it. This is a great way to test the model before integrating it into your application.

![Chat playground](chat_playground.png)

## Consume from .NET

## Consume from SemanticKernel

## Conclusion