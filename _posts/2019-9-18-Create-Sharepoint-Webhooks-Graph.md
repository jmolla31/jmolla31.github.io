---
layout: post
title: Creating sharepoint webhooks using the Microsoft Graph API
---

Believe, I almost went **FXXXX CRAZY** trying to get this working on a recent project.

![](https://raw.githubusercontent.com/jmolla31/jmolla31.github.io/master/images/avril-no-no-no-no.gif)


# Microsoft Graph

I like the idea behind Graph (really, we use it in our projects and works fairly good although some features still need some elbow grease from the dev-team.).

Having a centralized entrypoint to the "suite" of Microsoft services that's REST oriented, with a good SDK (.NET Core in this case) and with somewhat of a fluent-API model (memberOf stil doesn't work for users...DUH) is great and provides easy integration for apps but some features can be a bit confusing or "tricky" to get working.

*This may be one of those things that when you already know it seems stupid simple but believe me...I wasted almost two days to get it working*

# Subscriptions are the new webhooks

Wanting to create a new Sharepoint Webhook so you get notified when a new Avril Lavigne gif gets uploaded?

Way 1...do a wrapper over the Sharepoint api or just hardCode the Http call and call it a day.

Way 2...have some intern running around yelling that a new file has been uploaded, but HR may not like that.

Way 3...use the Graph API you already have integrated into your project to avoid coupling to yet-another-external-service. 

3 looks good? Welcome to the world of suscriptions and drives.


# Creating a Graph subscription

Graph subscription objects are designed to work over a wide range of resources and services so we need them to "configure" them a bit.

The main fields of a subscription creation are:

- ChangeType => What changes are going to trigger the subscription (updated,created,deleted)
- NotificationUrl => Really, I need to explain this one?
- Resource => What resource is going to be monitored for changes
- ExpirationDateTime => Subscription lifetime, this changes from resource to resource but usually is something between 30 and 180 days.

When accessing Sharepoint resources over Graph everything is considered as a "Drive" or "DriveItem" (hi there, OneDrive for Business) so we have some constraints over what we can do.

First of all, you can only set a subscription over the root of a sharepoint library so the subscription will trigger for every change in any underlying item or folder.

Because of this, only the ChangeType "updated" is valid.


*(you need to know the driveId of the sharepoint library that you want to monitor...graphExplorer is very useful for this)*

And aaaaaaalso, the max ExpirationDateTime is one month from the subscription creation date. You need to implement your own refresh/renewal service.

Example of a valid Subscription model in C#:

```csharp
var subscription = new Subscription
{
    ChangeType = "updated",
    NotificationUrl = notificationUrl,
    Resource = $"drives/{driveId}/root",
    ExpirationDateTime = DateTime.UtcNow.AddDays(30)

};
result = await this.graphStableClient.Subscriptions.Request().AddAsync(subscription);
```
