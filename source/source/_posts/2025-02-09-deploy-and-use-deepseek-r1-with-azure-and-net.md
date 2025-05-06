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
disqusId: deploy-and-use-deepseek-r1-with-azure-and-net
---

{% asset_img deepseek-azure.png DeepSeek Azure %}

DeepSeek models have taken technological world by surprise, demonstrating that cutting-edge AI development is no longer confined to the certain valley made out of silicon, but has become a global phenomenon. Although Microsoft has traditionally partnered with OpenAI, the users of its technologies still have reasons to be optimistic. The Azure cloud platform recently announced support for the DeepSeek R1 model through its Azure AI Foundry service. Currently in public preview, the model may be run in serverless mode and is free of charge. This article will guide you through deploying the R1 model and integrating it with .NET applications.

<!-- more -->

## Deploy DeepSeek R1 on Azure AI Foundry

Deploying the DeepSeek model on Azure is straightforward, even for those new to Azure AI Foundry (formerly Azure AI Studio).

Start by creating a new hub, which serves as a container for your AI applications and models. This can be done via AI Foundry [Management Center](https://ai.azure.com/managementCenter/allResources). Note that the region you select for your hub will impact model availability. As of February 2025, the DeepSeek R1 model is available in East US, East US 2, West US, West US 3, South Central US, and North Central US regions only.

![Creating Azure AI Foundry hub](create-hub.png)

Next, you need to create a new project. In the [Management Center](https://ai.azure.com/managementCenter/allResources) select the hub you created, and click on "New project" button. Provide a name for your project and click "Create." Your project will be ready in a few seconds.

![Creating Azure AI Foundry project](create-project.png)

Once you have your hub and project ready, you can deploy DeepSeek R1 model. Navigate to the [Model catalog](https://ai.azure.com/explore/models) tab within your project. Search for the "DeepSeek R1" model, and click "Deploy". Provide a region unique name for your model and optionally apply content filters. Click "Deploy" (again) to start provisioning the model, which may take a few minutes.

![Deploying DeepSeek R1 model](deploy-model.png)

After deployment finishes, you will find the model in the [Models + endpoints](https://ai.azure.com/build/deployments/model) tab of your project. Select the deployment name to access detailed information, including the endpoint URL and API key, which are necessary for programmatic consumption.

![DeepSeek R1 deployment details](deployment-info.png)

Use the chat playground available in the [Playgrounds](https://ai.azure.com/playgrounds) tab of your project to ensure the deployment is functioning correctly. Make sure to select DeepSeek R1 deployment before starting the conversation. This step helps verify that the model will work seamlessly when integrated programmatically.

![Chat playground](chat-playground.png)

## Consume from .NET

Models deployed via Azure AI Foundry can be accessed from any programming language that supports HTTP requests. For .NET, Azure provides an SDK through the [Azure AI Inference](https://www.nuget.org/packages/Azure.AI.Inference) library. To consume the model, create a chat client using the deployment endpoint URL and API key, and then run chat completion.

<script src="https://gist.github.com/uveta/250385b53805f8c2859ec1b813d42b27.js"></script>

## Consume from Semantic Kernel

For more complex applications using Semantic Kernel, consuming models deployed in Azure AI Foundry is straightforward. Utilize the [Microsoft.SemanticKernel.Connectors.AzureAIInference](https://www.nuget.org/packages/Microsoft.SemanticKernel.Connectors.AzureAIInference) connector library. Register the AI Inference connector using the deployment name, endpoint URL, and API key while building the kernel. Once configured, provision the kernel and use the `IChatCompletionService` service to run chat completion.

<script src="https://gist.github.com/uveta/2c717c8d6b94215bd2a61ca3e9878e4d.js"></script>

## Conclusion

Complete .NET and Semantic Kernel chat samples are available on [GitHub](https://github.com/uveta/demo-azure-deepseek). Make sure you add the deployment name, endpoint URL, and API key where indicated in the code to run the applications without issues.

Keep in mind that the DeepSeek R1 model on Azure is still in preview and is subject to throttling and rate limiting. While it may take from couple of seconds up to few minutes to receive a meaningful response, the service is currently free, allowing for extensive experimentation.
