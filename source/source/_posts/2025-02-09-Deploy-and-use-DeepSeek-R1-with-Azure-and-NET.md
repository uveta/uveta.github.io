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

{% asset_img deepseek-azure.png DeepSeek Azure %}

DeepSeek models have surely taken technological and financial world by storm, showing that state-of-the-art AI development is not limited to the specific valley made out of silicone, but has become a global affair. Although Microsoft has traditionally partnered with OpenAI, for folks using it's technologies it is not all doom and gloom. Actually, there are some great news! Azure cloud platform already supports DeepSeek R1 model, via it's Azure AI Foundry service. The model is in public preview, but it can be run in serverless mode and it's usage is free of charge, for now. Let's see how to deploy R1 model, and how to consume it from .NET applications.

<!-- more -->

## Deploy

Getting DeepSeek model up and running on Azure is quite simple, even if you never used Azure AI Foundry (formerly known as Azure AI Studio). First, you need to create a new hub and project, which act as a container for your AI applications and models. This is done via AI Foundry [Management center](https://ai.azure.com/managementCenter/allResources). Pay attention to the region you are creating hub and project in, as this will affect model availability. As of February 2025, DeepSeek R1 is only available in East US, East US 2, West US, West US 3, South Central US, and North Central US regions.

![Creating Azure AI Foundry hub](create-hub.png)

![Creating Azure AI Foundry project](create-project.png)

Once you have your hub and project created, you can deploy DeepSeek R1 model. This is done via the [Model catalog](https://ai.azure.com/explore/models) tab in your project. Search for the "DeepSeek R1" model, and then click on "Deploy" button. You will be asked to provide a globally unique name for your model, and to optionally use content filters. Once you click "Deploy", Azure will start deploying the model, which can take a few minutes.

![Deploying DeepSeek R1 model](deploy-model.png)

Once the model is deployed, you will find it in your project's [Models + endpoints](https://ai.azure.com/build/deployments/model) tab. Select the deployment to get more information about it. Make note of the endpoint URL and API key, as we will use them later to consume the model programmatically.

![DeepSeek R1 deployment info](deployment-info.png)

<!-- Playground -->
To make sure deployment is working as expected, you can use chat playground, which is available in the [Playgrounds](https://ai.azure.com/playgrounds) tab of your project. Select the DeepSeek R1 deployment, and then you can start chatting with it. This is a great way to test the model before integrating it into your application.

![Chat playground](chat-playground.png)

## Consume from .NET

Models deployed via Azure AI Foundry can be consumed from any programming language, if it supports HTTP requests. However, in case of .NET, we don't have to code the HTTP communication ourselves, as Azure provides SDK via [Azure AI Inference library](https://www.nuget.org/packages/Azure.AI.Inference). Consuming the model is farily easy: we must create a chat client, using deployment endpoint URL and API key, and run chat completion.

<script src="https://gist.github.com/uveta/250385b53805f8c2859ec1b813d42b27.js"></script>

## Consume from SemanticKernel

[Microsoft.SemanticKernel.Connectors.AzureAIInference NuGet](https://www.nuget.org/packages/Microsoft.SemanticKernel.Connectors.AzureAIInference)

<script src="https://gist.github.com/uveta/2c717c8d6b94215bd2a61ca3e9878e4d.js"></script>

## Conclusion

Both .NET and SemanticKernel examples can be found in the [GitHub](https://github.com/uveta/demo-azure-deepseek). Make sure to add deployment name, endpoint URL, and API key where indicated in the code, and you should be able to run the samples without any issues. Bear in mind that DeepSeek R1 model on Azure is still in its infancy, so sometimes it might take couple of minutes to get a meaningful response.