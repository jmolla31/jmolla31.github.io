---
layout: post
title: Using arrays of objects in AppService configurations.
---

Oooh my beloved configs...this may sound trivial but I lost quite a bit of time today with it so here it is to avoid you doing the same.

![](https://raw.githubusercontent.com/jmolla31/jmolla31.github.io/master/images/avril-heart-hands.gif)


*The baseline here is that we're working with a dotnet web API deployed to the AppService*

Let's say that you have some configuration in your Visual Studio secrets that contains an array of objects. In my case the array defines all the authorities that some *Microsoft Graph* client I have can login to:

```json
"MicrosoftGraph": {
    "Authorities": [
      {
        "Name" :  "default",
        "Identifier": "UnderMySkin"
      },
      {
        "Name": "BlackStar",
        "Identifier": "GoodbyeLullaby"
      }
    ],
    "ClientId": "LetGo",
    "ClientSecret": "TheBestDamnThing"
  },
```

Inside the C# project this config maps to an **IEnumerable\<Authorities\>** without any problems but-how-da-fuq-in-thiz-life can we define that in the *Configuration* blade of the AppService?

As you probably already know, JSON "hierarchy" in AppService settings is defined using double under-scores: '__' so...to define elements of an array or list we just use number indexes starting at 0:

**MicrosoftGraph__Authorities__0__Name** => default
**MicrosoftGraph__Authorities__0__Identifier** => UnderMySkin
**MicrosoftGraph__Authorities__1__Name** => BlackStar
**MicrosoftGraph__Authorities__1__Identifier** => GoodbyeLullaby

Screenshot from the Azure portal:

![](https://raw.githubusercontent.com/jmolla31/jmolla31.github.io/master/images/azure_config_1.PNG)