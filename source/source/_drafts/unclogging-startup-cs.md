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

<!-- more -->

_This post is part of [C# Advent Calendar 2021](https://www.csadvent.christmas/). Cheers to [Matthew D. Groves](https://twitter.com/mgroves) for letting me participate!_

Configuring service container and setting up a request pipeline in ASP.NET Core can get quite complex, especially for larger projects. A well-established way of doing this was using _Startup.cs_ and its _ConfigureServices_ and _Configure_methods. Even in this case it is worthwille following Single-responsibility principle, and try not to cram complete application setup in a single class. I wanted to show you a way how to avoid this. Although .NET 6 has rolled in and removed the need of having _Startup.cs_ altogether, I think the ideas presented in this post are relevant, even in the new era.

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