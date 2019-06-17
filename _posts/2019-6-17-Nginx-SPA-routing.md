---
layout: post
title: Serving and routing SPA's with Nginx and Docker
---

We all love SPA's and Docker right?

![](https://raw.githubusercontent.com/jmolla31/jmolla31.github.io/master/images/this.gif)

And we know how to serve one using a minimal/alpine container with nginx...light and fast!

```Dockerfile
FROM nginx:stable-alpine
COPY ./build /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```


But if you've ever used this approach and served an SPA from a container using a default nginx setup you also know that if you try to access a route outside of the main entrypoint (*www.yourDumbHost.com/users/details*) nginx doesn't know where to match that requested route and responds with a pretty 404 Status code.

The solution relies in modifying the **default.conf** so our nginx instance redirects all requests to *index.html* and our SPA code can decide than what to do next.

To achieve this we're going to add an extra line to our Dockerfile to copy the "updated" config file into our container image:

```Dockerfile
FROM nginx:stable-alpine
COPY ./build /usr/share/nginx/html
COPY default.conf /etc/nginx/conf.d/
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

**default.conf**

```json
server {
    listen   80; ## listen for ipv4; this line is default and implied
    listen   [::]:80 default ipv6only=on; ## listen for ipv6

    root /usr/share/nginx/html;
    index index.html;

    server_tokens  off; # disable the Server nginx header 

    server_name _; # all hostnames

    # enable gzip
    gzip on;
    gzip_disable "msie6";

    gzip_comp_level 6;
    gzip_min_length 1100;
    gzip_buffers 16 8k;
    gzip_proxied any;
    gzip_types
        text/plain
        text/css
        text/js
        text/xml
        text/javascript
        application/javascript
        application/x-javascript
        application/json
        application/xml
        application/rss+xml
        image/svg+xml;

    location / {
        try_files $uri /index.html; # redirect all request to index.html
    }
}
```




How you provide the modified **default.conf** file to the docker runner is up to you, it can be injected into a CI pipeline like GitlabCI or Azure DevOps or you can just add it to the repository.

I personally prefer the last one since it allows me to make changes to the config and they're backed-up alongside the rest of the code.