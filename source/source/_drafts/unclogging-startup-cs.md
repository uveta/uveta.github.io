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

<!-- more -->

_This post is part of [C# Advent Calendar 2010](https://www.csadvent.christmas/). Cheers to [Matthew D. Groves](https://twitter.com/mgroves) for letting me participate!_

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