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

Configuring service container and setting up a request pipeline in ASP.NET Core can eat up a lot of lines of code, especially more complex projects. A well-established way of doing this is using _Startup.cs_ and its `ConfigureServices()` and `Configure()` methods. Even in this case it is worthwhile following Single-responsibility principle, and try not to cram complete application setup in a single class. I wanted to show you a way how to avoid this. Although .NET 6 has rolled in and removed the need of having _Startup.cs_ altogether, I think the ideas presented in this post are relevant, even in the new era.

<!-- more -->

_This post is part of [C# Advent Calendar 2021](https://www.csadvent.christmas/). Cheers to [Matthew D. Groves](https://twitter.com/mgroves) for letting me participate!_

## Basic principles

There are two .NET features that we will apply: [Options pattern](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options) and [Extension methods](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/extension-methods). Similar to how ASP.NET Core uses plugin architecture, we will utilize these features to separate concerns and leave heavy lifting to dependency injection container.

One design standard to keep in mind is that almost all parts of ASP.NET Core are configured via [IOptions<TOptions>](https://docs.microsoft.com/en-us/dotnet/api/microsoft.extensions.options.ioptions-1). And, since all `IOptions<TOptions>` instances are part of service container, we can use [IConfigureOptions<TOptions>](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options#use-di-services-to-configure-options) or [IPostConfigureOptions<TOptions>](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options#options-post-configuration) to override any one of them.

## MVC

// MvcExtensions.cs
public static IServiceCollection AddMvc(this IServiceCollection services)
{
	services.AddControllers();
	services.AddSingleton<IConfigureOptions<MvcOptions>, ConfigureMvcOptions>();
	return services;
}

// ConfigureMvcOptions.cs
public class ConfigureMvcOptions: IConfigureOptions<MvcOptions>
{
	public void Configure (MvcOptions options)
	{
	}
}

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
	Cors
	  AddCors
	  CorsOptions
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
	SPA, StaticFiles, DefaultFiles
<!-- more -->