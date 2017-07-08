+++
comments = true
tags = ["security","tech", "english"]
title = "An AppSec Reminder: CORS"
draft = true
share = true
slug = "appsec-reminder-cors"
date = "2017-01-29T15:34:24+11:00"
image = "images/post-cover.jpg"

+++
<br>

This post is part of a series that started [here](/post/appsec). In this post I will look into the details of CORS _(pun not intended)_. What is it? and, what does it solve? <!--more-->

### CORS

Cross Origin Resource Sharing (CORS) is a safe exception to the single origin policy ([SOP]). It is a mechanism that allows a website to make a request to a server in a different [origin].

**Scenario:**

Maggie deployed a static website in `https://dev-cats-dev.me` and she wants to display some blog posts she already has in `http://api.damngoodwriter.mx`. But the browser keeps blocking the request to get the posts because they're in a different origin: `https://dev-cats-dev.me != http://api.damngoodwriter.mx`.

< picture goes here >

**How does CORS help?**

**Browsers** (and browsers only!) will check the [origin] of the website making the request, and the origin where the request is going. If they match, the browser will allow the request, if they don't match, then the browser won't make the request. _(unless it is an allowed [CORS] request)_.


[origin]: https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy#Definition_of_an_origin
[SOP]: /post/appsec-reminder-sop
