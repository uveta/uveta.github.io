---
title: Using GitHub workflow to deploy Unity project to Azure Static Web Apps
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
date: 2022-12-19 11:17:46
---


{% asset_img github.jpg GitHub %}

This post explains how to create a GitHub workflow, which builds, tests and deploys a Unity project to Azure Static Web Apps (SWA) instance. Due to foundations on which SWA was built upon, it represents a perfect way to develop and test any game built on Unity WebGL platform.

<!-- more -->

_This post is part of **.NET Advent 2022!** Check other great entries [here](https://dotnet.christmas/)._

## Introduction

I created this workflow as a part of another activity. It involved remastering Hungry Square, a game I once developed, into Unity. If you want to have the whole context, check out [my other post, about how I learned Unity in 21 days](../unity-in-21-days).

## CI/CD Pipeline

Forget that we are actually working with Unity. Lets consider our game project just another web application. A well-established way to write a web app CI/CD pipeline is to break it into three steps: project build, infrastructure deploy, and application bundle deploy. Lets take a deeper look into each one.

### Building and testing Unity project

We're starting off in uncharted territory, at least for me. Building Unity projects was something I was not too familiar with. Luckily, good folks at [game.ci](https://game.ci/docs/) provide a convenient way how to work with Unity games, in GitHub environment. Setting up a build job is straightforward, and involves following steps:

- Ensure Unity license, username, and password are provided to workflow, via GitHub secrets. Details on activating license and setting up secrets, depending on license type, can be found in [game.ci documentation](https://game.ci/docs/github/activation)
- Run any tests in project using [game-ci/unity-test-runner](https://game.ci/docs/github/test-runner) action.
- Build project for all configured platforms using [game-ci/unity-builder](https://game.ci/docs/github/builder) action.
- Optionally, use [actions/cache](https://github.com/actions/cache) action to cache dynamic project files. This often reduces build time dramatically.
- Finally, make build output available to succeeding jobs via [actions/upload-artifact](https://github.com/actions/upload-artifact).

<script src="https://gist.github.com/uveta/16b5583b4d2961cd16994404320f1f10.js"></script>

If all steps have finished without an error, we should end up with an artifact containing our application bundle.

### Infrastructure

When setting up this type of projects, we should make no assumptions about existing deployment environment. It is why I have included a job to create all necessary infrastructure. There is an abundance of ways how to programmatically deploy to Azure (CLI, PowerShell, Terraform, etc.), each one with its pros and cons. In our case, the optimal way was using Bicep domain-specific language, which offers a good balance between complexity, robustness and extensibility. For this purpose, we will deploy a resource group, in which a SWA instance will be created. All necessary templates can be found in [./infra](https://github.com/uveta/hungry-square-unity/tree/main/infra) directory.

As with almost all actions in Azure, Bicep deployment has to be preceded with an Azure login step. Detailed instructions on how to setup subscription identifier and login credentials, using GitHub secrets, can be found in [Azure login action documentation](https://github.com/Azure/login). Subsequent Bicep deployment is done using [azure/arm-deploy](https://github.com/Azure/arm-deploy) action.

<script src="https://gist.github.com/uveta/c4542f2218751bb128ffa48b4106c06d.js"></script>

### Deploying to Azure Static Web Apps

Deployment of application bundle to a SWA instance consists of two steps (or four if you are nitpicking and want to include artifact download and Azure login). Prior to actual upload, a deployment token for SWA must be obtained. We will use [Azure CLI](https://github.com/Azure/cli) to get it and store it in a job environment variable _SWA\_DEPLOYMENT\_TOKEN_.

Uploading build output to SWA is done using [azure/static-web-apps-deploy](https://github.com/Azure/static-web-apps-deploy) action. In our case, following parameters have to be included:

- **azure_static_web_apps_api_token**, a deployment token obtained in previous step
- **repo_token**, a GitHub token with appropriate permissions, which action will use to provide URL to access our application
- **app_location**, directory or file to upload to SWA, which is in our case _./build/WebGL/WebGL_

<script src="https://gist.github.com/uveta/00d40c2d5ddae36762a05cb0b1f8fe94.js"></script>

### Bonus (environment tear down)

After a pull request is merged or abandoned, and a feature branch was deleted, it is appropriate to clean up associated SWA environment. This can easily be done via [tear down workflow](https://github.com/uveta/hungry-square-unity/blob/main/.github/workflows/teardown.yaml), which also uses [SWA action](https://github.com/Azure/static-web-apps-deploy). Similar to code upload, a deployment token has to be obtained first. Afterwards, an SWA action with action parameter set to _close_ is executed, which will remove environment associated with current git branch.

<script src="https://gist.github.com/uveta/6438618e956a75217f42915bd007a6fd.js"></script>

## Final thoughts

After spending couple of days, making sure that the project is built and deployed properly, I can give you some tips to make designing your future workflow more smooth:

- Make sure you add _.gitattributes_ with proper Git LFS definitions early, ideally before including any asset. Although git makes migration of committed assets to LFS super convenient, it requires rewriting branch history and force pushing it. Not something you really want to do, if you have a whole team collaborating on a project.
- Unity build action will fail, if source files were modified after checkout. As a last resort, you can force execution of a dirty build, using _game-ci/unity-builder_ action parameters.
- Double-check all included scenes in Unity Editor, in File->Build Settings->Scenes In Build. In case of any issue with scenes, build error will not help you much.
- It might not be clear what is the correct path of application bundle in build artifact, as it is located under a couple of levels of directories. Verify path by manually downloading artifact bundle, and adjust _app_location_ parameter of _azure/static-web-apps-deploy_ step accordingly.

## Conclusion

There you have it, a workflow to help you get any Unity WebGL project up and running in minutes. You can check out the final CI/CD workflow [here](https://github.com/uveta/hungry-square-unity/blob/main/.github/workflows/ci.yaml).
