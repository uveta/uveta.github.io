---
title: Deploy Unity games to Azure Static Web Apps, using GitHub actions
tags:
  - advanced
  - dotnet
  - azure
  - unity
  - github
  - devops
categories:
  - blog
disqusId: github-workflow-unity-azure-static-webapps
---

{% asset_img github-workflow.jpg GitHub Workflow %}

This post explains how to create a GitHub workflow, which would build, test and deploy a Unity project to Azure Static Web Apps instance. Due to nature of Azure Static Web Apps, it is a convenient way to develop and test any game, built for Unity WebGL platform.

<!-- more -->

_This post is part of **.NET Advent 2022!** Check other great entries [here](https://dotnet.christmas/). It is also a companion post to [how I learned Unity in 21 days](./unity-in-21-days.md)_


## Building and testing Unity project

## Infrastructure

This workflow makes no assumptions about existing deployment environment. It is why I am including a job to create necessary infrastructure. Most convenient way to deal with infrastructure in Azure is, of course, using Bicep templates. For this purpose, we will deploy a resource group, in which a Static Web App instance will be created as well. All necessary templates can be found in [./infra](https://github.com/uveta/hungry-square-unity/tree/main/infra) directory.

As with almost all actions in Azure, deployment as well has to be preceded with a [login step](https://github.com/Azure/login). Detailed instructions on how to setup subscription identifier and login credentials, using GitHub repository or environment secrets, can be found in [login action documentation](https://github.com/Azure/login).

## Deploying to Azure Static Web Apps

The actual deployment of build output, to SWA instance, consists of two steps (or four if you are nitpicking and want to include artifact download and Azure login). Prior to actual upload, a deployment token for SWA must be obtained first. We will use Azure CLI to obtain it, and store it in job environment variable.

Uploading build output to SWA is done using [official SWA action](https://github.com/Azure/static-web-apps-deploy). For our case, following parameters have to be included:

- **azure_static_web_apps_api_token**, a deployment token obtained in previous step
- **repo_token**, a GitHub token with appropriate permissions, so a URL can be provided as a message in pull request
- **app_location**, directory or file to upload to SWA, which is in our case _./build/WebGL/WebGL_

## Bonus (environment tear down)

After a pull request has been merged or abandoned, and a feature branch was deleted, it is appropriate to clean up associated SWA environment. This can easily be done via [tear down workflow](https://github.com/uveta/hungry-square-unity/blob/main/.github/workflows/teardown.yaml), which also uses [SWA action](https://github.com/Azure/static-web-apps-deploy). Similar to code upload, a deployment token has to be obtained first. Afterwards, a _close_ action on SWA step has to be executed, which will remove environment associated with current git branch.

## Conclusion
