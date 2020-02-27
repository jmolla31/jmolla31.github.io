---
layout: post
title: Prevent Clickjacking on you Nginx-hosted website
---

No one wants to see Avril stealing their taxi...and you don't want anyone embedding and stealing your website ;)

![](https://raw.githubusercontent.com/jmolla31/jmolla31.github.io/master/images/avril-taxi.gif)

## Introduction

But before anything else...what the hell is clickjacking dude?

In a nutshell, clickjacking (https://en.wikipedia.org/wiki/Clickjacking) is a type of attack that consists in embedding your website onto another malicious site, that puts a layer on top of what appears to be your completely normal site...and the rest is history.

## Fix

To prevent this, a very simple fix can be implemented, the X-Frame-Options header needs to be set to **"SAMEORIGIN"**.

This header only allows the site to be embedded/displayed on a frame from the same origin as the site itself.

To apply this header to a normal Nginx deployment we need to add this line to our default.conf file:

```
add_header X-Frame-Options SAMEORIGIN;
```

Restart/redeploy your Nginx server and there you go.

Also, if you want to check the security "rating" of your site or deployment go to https://www.ssllabs.com/ssltest

Bye bitchez!
