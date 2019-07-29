---
layout: post
title: Breaking the Dotnet request pipeline inside of a filter.
---

*Just a brief extension of the last post...*

What if you want to 'break' or 'short-circuit' a request so it never gets to the a action in the controller?

Just set the **Result** property of the **ActionExecutingContext** inside the filter method and Dotnet will short-circuit the pipeline and return a response immediately to the caller.

```csharp
context.Result = new StatusCodeResult((int)HttpStatusCode.BadRequest);
```

There you go ;)