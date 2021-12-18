---
title: Unclutter Startup.cs
tags:
  - advanced
  - dotnet
  - azure
categories:
  - blog
disqusId: unclutter-startup-cs
date: 2021-12-18 15:00:09
---


{% asset_img calendar.jpg Advent calendar %}

Configuring service container and setting up request pipeline in ASP.NET Core can eat up a lot of lines of code, especially for more complex projects. A well-established way of doing this is using _Startup.cs_ and its `ConfigureServices()` and `Configure()` methods. Even in this case it is worthwhile following Single-responsibility principle, and try not to pack complete application setup in a single class. I wanted to show you a way how to prevent startup from growing uncontrollably. One disclaimer though. ASP.NET Core 6 has rolled in and removed the need of having _Startup.cs_ altogether. Despite that fact, I am sure the ideas presented in this post will be relevant, even in the new era.

<!-- more -->

_This post is part of [C# Advent Calendar 2021](https://www.csadvent.christmas/). Cheers to [Matthew D. Groves](https://twitter.com/mgroves) for letting me participate!_

## Basic principles

We will apply two .NET features: [Options pattern](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options) and [Extension methods](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/extension-methods). Similar to how ASP.NET Core uses plugin architecture, we will utilize these features to separate concerns and leave heavy lifting to dependency injection container.

One design standard to keep in mind is that almost all parts of ASP.NET Core are configured via [IOptions<TOptions>](https://docs.microsoft.com/en-us/dotnet/api/microsoft.extensions.options.ioptions-1) pattern. And, since all `IOptions<TOptions>` instances are part of service container, we can use [IConfigureOptions<TOptions>](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options#use-di-services-to-configure-options) or [IPostConfigureOptions<TOptions>](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options#options-post-configuration) to override any one of them.

## MVC

Whether you are using Controllers only, or including Views and Razor Pages, MVC setup is more or less the same. The goal is to call [AddControllers()](https://docs.microsoft.com/en-us/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addcontrollers), [AddControllersWithViews()](https://docs.microsoft.com/en-us/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addcontrollerswithviews) or [AddRazorPages()](https://docs.microsoft.com/en-us/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addrazorpages), based on your scenario, and define configuration for [MvcOptions](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions), and possibly [RazorPagesOptions](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.mvc.razorpages.razorpagesoptions). If you are using [System.Text.Json](https://docs.microsoft.com/en-us/dotnet/api/system.text.json.jsonserializer) or [Newtonsoft Json.NET](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson) for model serialization, you could also configure them in a similar manner. Whole setup would look like this:

```csharp
public static class MvcExtensions
{
    public static IServiceCollection ConfigureMvc(this IServiceCollection services)
    {
        services.AddControllers().AddNewtonsoftJson();
        services.AddSingleton<IConfigureOptions<MvcOptions>, ConfigureMvcOptions>();
        services.AddSingleton<IConfigureOptions<MvcNewtonsoftJsonOptions>, ConfigureNewtonsoftOptions>();
        return services;
    }
}

public class ConfigureMvcOptions : IConfigureOptions<MvcOptions>
{
    public void Configure(MvcOptions options)
    {
        options.SuppressAsyncSuffixInActionNames = true;
    }
}

public class ConfigureNewtonsoftOptions : IConfigureOptions<MvcNewtonsoftJsonOptions>
{
    public void Configure(MvcNewtonsoftJsonOptions options)
    {
        options.SerializerSettings.Converters.Add(new StringEnumConverter());
    }
}
```

## Authentication

Completely driving authentication by configuration is difficult. You need to determine what type of authentication your application uses in advance. Good news is that, once baseline is established, you can use above mentioned technique to configure individual parts. For example, you would use [CookieAuthenticationOptions](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions) to configure cookies, and [JwtBearerOptions](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.authentication.jwtbearer.jwtbeareroptions) for token based authentication. Extension and individual configuration options, including an example of injecting IConfiguration into one of them, would look like this:

```csharp
public static class AuthenticationExtensions
{
    public static IServiceCollection ConfigureAuthentication(this IServiceCollection services)
    {
        services.AddAuthentication().AddCookie().AddJwtBearer();
        services.AddSingleton<IConfigureOptions<AuthenticationOptions>, ConfigureAuthenticationOptions>();
        services.AddSingleton<IConfigureOptions<CookieAuthenticationOptions>, ConfigureCookieAuthenticationOptions>();
        services.AddSingleton<IConfigureOptions<JwtBearerOptions>, ConfigureJwtBearerOptions>();
        return services;
    }
}

public class ConfigureAuthenticationOptions : IConfigureOptions<AuthenticationOptions>
{
    public void Configure(AuthenticationOptions options)
    {
        options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
    }
}

public class ConfigureCookieAuthenticationOptions : IConfigureOptions<CookieAuthenticationOptions>
{
    public void Configure(CookieAuthenticationOptions options)
    {
        options.ExpireTimeSpan = TimeSpan.FromHours(1);
    }
}

public class ConfigureJwtBearerOptions : IConfigureOptions<JwtBearerOptions>
{
    private readonly IConfiguration _configuration;

    public ConfigureJwtBearerOptions(IConfiguration configuration)
    {
        _configuration = configuration;
    }

    public void Configure(JwtBearerOptions options)
    {
        options.Authority = _configuration.GetValue<string>("jwt:authority");
        options.RequireHttpsMetadata = false;
        options.IncludeErrorDetails = true;
    }
}
```

## Authorization

Configuring authorization can get quite tedious, if number or complexity of policies increases. Luckily, setup process is pretty straightforward, as the only concern is injecting configuration via [AuthorizationOptions](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions) type.

```csharp
public static class AuthorizationExtensions
{
    public static IServiceCollection ConfigureAuthorization(this IServiceCollection services)
    {
        services.AddAuthorization();
        services.AddSingleton<IConfigureOptions<AuthorizationOptions>, ConfigureAuthorizationOptions>();
        return services;
    }
}

public class ConfigureAuthorizationOptions : IConfigureOptions<AuthorizationOptions>
{
    public void Configure(AuthorizationOptions options)
    {
        options.DefaultPolicy = new AuthorizationPolicyBuilder().RequireAuthenticatedUser().Build();
    }
}
```

## OpenAPI

If you care about your REST API consumers, you are publishing service description using OpenAPI specification. And since we are talking about ASP.NET Core, you are probably using awesome [Swashbuckle](https://www.nuget.org/packages/Swashbuckle.AspNetCore) package. As with previously seen framework parts, even this extension can be setup in a similar fashion. Whether its generating definition file using `SwaggerGenOptions`, or adjusting swagger user interface via `SwaggerUIOptions`, everything can be broken down and put into separate types. 

```csharp
public static class OpenApiExtensions
{
    public static IServiceCollection ConfigureOpenApi(this IServiceCollection services)
    {
        services.AddSwaggerGen();
        services.AddSwaggerGenNewtonsoftSupport();
        services.AddSingleton<IConfigureOptions<SwaggerGenOptions>, ConfigureSwaggerGenOptions>();
        services.AddSingleton<IConfigureOptions<SwaggerOptions>, ConfigureSwaggerOptions>();
        services.AddSingleton<IConfigureOptions<SwaggerUIOptions>, ConfigureSwaggerUiOptions>();
        return services;
    }
}

public class ConfigureSwaggerGenOptions : IConfigureOptions<SwaggerGenOptions>
{
    public void Configure(SwaggerGenOptions options)
    {
        options.SwaggerDoc("v1", new OpenApiInfo { Title = "Demo", Version = "v1" });
    }
}

public class ConfigureSwaggerOptions : IConfigureOptions<SwaggerOptions>
{
    public void Configure(SwaggerOptions options)
    {
        options.RouteTemplate = "swagger/{documentName}/swagger.json";
        options.PreSerializeFilters.Add((swaggerDoc, httpReq) =>
        {
            swaggerDoc.Servers = new[] {
                new OpenApiServer {
                    Url = $"{httpReq.Scheme}://{httpReq.Host.Value}{httpReq.PathBase}",
                    Description = "Default"
                }
            };
        });
    }
}

public class ConfigureSwaggerUiOptions : IConfigureOptions<SwaggerUIOptions>
{
    public void Configure(SwaggerUIOptions options)
    {
        options.SwaggerEndpoint("/swagger/v1/swagger.json", "Demo v1");
    }
}
```

## Application Insights

How about some Azure extensions? Most of the time you will not even need them, as ASP.NET Core seamlessly integrates with most of Azure resources. However, one particular extension I recommend using are [Application Insights](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). You want to have application telemetry under control, and this package allows you to fine tune metrics and data that will be ingested by Azure. From setup point of view, there is nothing surprising:

```csharp
public static class TelemetryExtensions
{
    public static IServiceCollection ConfigureTelemetry(this IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();
        services.AddSingleton<IConfigureOptions<ApplicationInsightsServiceOptions>, ConfigureApplicationInsights>();
        return services;
    }
}

public class ConfigureApplicationInsights : IConfigureOptions<ApplicationInsightsServiceOptions>
{
    public void Configure(ApplicationInsightsServiceOptions options)
    {
        options.EnableHeartbeat = true;
        options.EnableAppServicesHeartbeatTelemetryModule = true;
    }
}
```

## Extras

You've probably gotten the gist of it by now; use extension methods to extract service definitions, and options to do actual service configuration. For completeness, let me give you the rest of commonly used ASP.NET Core features, including types used for configuration.

* Default files middleware via [UseDefaultFiles()](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles) - [DefaultFilesOptions](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.builder.defaultfilesoptions)
* Static files middleware via [UseStaticFiles()](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles) - [StaticFileOptions](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions)
* Cross-Origin Resource Sharing via [AddCors()](https://docs.microsoft.com/en-us/dotnet/api/microsoft.extensions.dependencyinjection.corsservicecollectionextensions.addcors) - [CorsOptions](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.cors.infrastructure.corsoptions?view=aspnetcore-6.0)
* API Versioning services via [AddApiVersioning() and AddVersionedApiExplorer()](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Versioning) - [ApiExplorerOptions](https://github.com/dotnet/aspnet-api-versioning/blob/master/src/Microsoft.AspNetCore.Mvc.Versioning.ApiExplorer/ApiExplorerOptions.cs) and [ApiVersioningOptions](https://github.com/dotnet/aspnet-api-versioning/blob/master/src/Common/Versioning/ApiVersioningOptions.cs)

## Application code

Last but not least, what about your own application code? I highly recommend following the same principles, in order to avoid having application setup in a single place. If you are using clean architecture, and have your code decoupled into layers, initialization could be as simple as:

```
services
    .AddDomain()
    .AddApplicationServices()
    .AddRepositories()
    .AddIntegration();
```

## Conclusion

I have shown you one neat little trick that I use all the time, when setting up any kind of application. It is important to remember Single-Responsibility principle, even for this type of code. Having Startup consisting of several hundreds, or even thousands lines, is nothing unheard of. And, sadly, is an obvious sign of a code smell.

All code shown in this post was published to [GitHub](https://github.com/uveta/demo-unclutter-startup), as a single ASP.NET Core 6 project.