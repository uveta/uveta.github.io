---
title: Fix Semantic Kernel logging when using Dependency Injection
date: 2025-05-06 07:35:15
tags:
  - advanced
  - dotnet
  - csharp
  - ai
categories:
  - blog
disqusId: semantic-kernel-logging-when-using-di
---

{% asset_img dotnetmap.png Semantic Kernel %}

Integrating Semantic Kernel into ASP.NET applications is straightforward, due to built-in support for Dependency Injection (DI). However, while debugging an AI Agent issue, I discovered that all logging information from the Semantic Kernel was missing! This article explains the root cause of the issue and how to fix it.

<!-- more -->

## The Original Setup

<script src="https://gist.github.com/uveta/30994e4ecb61bbbbcc27024fc3277f81.js?file=Program.cs"></script>

<script src="https://gist.github.com/uveta/30994e4ecb61bbbbcc27024fc3277f81.js?file=Agent.cs"></script>

## The Problem

![No Logging](no-debug-log.png)

## The Solution

<script src="https://gist.github.com/uveta/30994e4ecb61bbbbcc27024fc3277f81.js?file=Program (fixed).cs"></script>

![Debug Logging](debug-log.png)