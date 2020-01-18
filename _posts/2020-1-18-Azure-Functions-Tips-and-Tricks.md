---
layout: post
title: Azure Functions tips and tricks 1, parameter parsing in http routes
---


I'm writing this one while waiting on Gatwick at 3:47 AM, with what has to be the worst avocado sandwich I've ever seen in my life sooooooooooooooo, bear with me.

![](https://raw.githubusercontent.com/jmolla31/jmolla31.github.io/master/images/avril-nobodyshome-guitar.gif)


(MS Ignite has been a blast, stalk me on twitter to know more about my not-so-well handled life.)


## Introduction

This is going to be a series of posts about little tricks and tips for dotnet core functions, short and to the point.


### Parameter parsing in Http routes

The Functions webjobs host includes routing functionality for http requests, meaning that we can define and bind parameters in the http route...just like a classic kestrel API.

This is a neat feature to write more API-like Functions projects but can produce some headaches.

The default binder works great as long as the input is correct but if you feed it a malformed request route...it will crash with a pretty nasty exception and 500 error code:

```
[18/01/2020 4:01:04] Executed 'GetAuthorComments' (Failed, Id=892d85bc-0a97-4800-b13f-ffdf7457f9a4)
[18/01/2020 4:01:04] System.Private.CoreLib: Exception while executing function: GetAuthorComments. Microsoft.Azure.WebJobs.Host: One or more errors occurred. (Exception binding parameter 'req') (Exception binding parameter 'author'). Exception binding parameter 'req'. System.Private.CoreLib: Input string was not in a correct format.
```

Although this can be considered "ok" behaviour (you malformed a request, here is your error dude!) there might be some cases where we want a default fallback value if the user messes up the request.

Also throwing and returning the exception is pretty computationally expensive compared to returning a nice and tidy 400 error and a "Request malformed, bla bla bla" response.


To avoid the binding exceptions just bind every parameter as a string and then perform any needed castings inside the function code.

Example endpoint that retrieves comments based on a provided Guid value:


```csharp
        [FunctionName(nameof(GetAuthorComments))]
        public async Task<IActionResult> GetAuthorComments(
           [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = "Comment/Author/{author}")] HttpRequest req, int author, ILogger log)
        {
            if (!Guid.TryParse(author, out var authorGuid))
            {
                return new BadRequestObjectResult("Malformed query uri");
            }

            return new OkObjectResult(this.myDummyService.GetComments(authorGuid));
        }
```
