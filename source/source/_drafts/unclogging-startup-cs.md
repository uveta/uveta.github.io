---
title: Unclogging Startup.cs
tags:
  - advanced
  - dotnet
  - azure
categories:
  - blog
disqusId: unclogging-startup-cs
---

{% asset_img calendar.jpg Advent calendar %}

Configuring service container and setting up a request pipeline in ASP.NET Core can eat up a lot of lines of code, especially for larger projects. A well-established way of doing this is using _Startup.cs_ and its _ConfigureServices()_ and _Configure()_ methods. Even in this case it is worthwhile following Single-responsibility principle, and try not to cram complete application setup in a single class. I wanted to show you a way how to avoid this. Although .NET 6 has rolled in and removed the need of having _Startup.cs_ altogether, I think the ideas presented in this post are relevant, even in the new era.

<!-- more -->

_This post is part of [C# Advent Calendar 2021](https://www.csadvent.christmas/). Cheers to [Matthew D. Groves](https://twitter.com/mgroves) for letting me participate!_

## Basic principles

## MVC

## Security

### Authentication

### Authorization

## Open API

## Application Insights

## Versioning

## Application

Last but not least, what about your own application code?

## Conclusion


toc
    .net 6 disclaimer
    tldr
    framework tools used (extension methods, options pattern)
      benefits - clear Startup.cs, dependency injection
    note about configuration and logging (serilog)
    MVC
      AddControllers(), AddControllersWithViews(), AddRazorPages()
      MvcOptions, RazorPagesOptions
      Serializers
        JsonOptions, MvcNewtonsoftJsonOptions
    API Versioning
      AddVersionedApiExplorer(), AddApiVersioning()
      ApiExplorerOptions, ApiVersioningOptions
    OpenAPI (Swagger)
      Swashbuckle package
      SwaggerGenOptions, SwaggerOptions, SwaggerUIOptions
    Application insights
      ApplicationInsightsServiceOptions
    Security
      Authentication
        AddAuthentication()
        AuthenticationOptions, CookieAuthenticationOptions, JwtBearerOptions
      Authorization
        AddAuthorization()
<!-- more -->