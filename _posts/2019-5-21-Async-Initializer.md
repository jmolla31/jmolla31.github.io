---
layout: post
title: Async Initializers in Dotnet Core 2.1
---

In normal DotNet Core API's and apps you only register "blank" services at startup, leaving them to be instantiated by the DI (Dependency Injection) container when needed depending on their lifetime. Even with singleton services, usually they accept a token or key from your settings/secrets, do some init actions and the service is up and running.

But what if you need to do some async actions in order to register and initialize the service? The solution we found is to use "Async Initializers" and the availiable async Main() entrypoints in C#7.1:

´´´csharp
      public static async Task Main(string[] args)
        {
            var host = CreateWebHostBuilder(args).Build();
            await host.InitAsync();
            host.Run();
        }
´´´


![_config.yml]({{ site.baseurl }}/images/config.png)

