---
layout: post
title: Async Initializers in Dotnet Core 2.1
---

### Introduction

In normal DotNet Core API's and apps you only register "blank" services at startup,leaving them to be instantiated by the DI (Dependency Injection) container when needed depending on their lifetime. 

Even with singleton services, usually they accept a token or key from your settings/secrets, do some init actions and the service is up and running.

But what if you need to do some async actions in order to register and initialize the service? The solution we found is to use "Async Initializers" and the availiable async Main() entrypoints in C#7.1:


```csharp
public static async Task Main(string[] args)
  {
      CreateWebHostBuilder(args).Build().Run();
  }
```

It can be done without it but this library by thomaslevesque provides a good helper plaftorm:
https://github.com/thomaslevesque/AspNetCore.AsyncInitialization/


### Usage


The concept is simple, we're going to create as many initializer classes as we need in our solution and they're going to be called at startup by our helper AsyncInitialization package. 

Our initializers should implement the *IAsyncInitializer* interface and the *Task InitializeAsync()* method.


Example of intializer class:

```csharp
public class AsyncInitializer : IAsyncInitializer
{
    private readonly someRequiredService service;

    public AsyncInitializer(someRequiredService service)
    {
        this.service = service;
    }

    public async Task InitializeAsync()
    {
        await this.service.InitializeThings();
    }
}
```

Then we call our initializers at Startup:

```csharp
services.AddAsyncInitializer<AsyncInitializer>();
```

Finally we need to tell the dotnet runtime to call our async initializers after the main WebHost has been built (remember that we injected *someRequiredService* into our initializer):

```csharp
public static async Task Main(string[] args)
{
    var host = CreateWebHostBuilder(args).Build();
    await host.InitAsync();
    host.Run();
}
```

