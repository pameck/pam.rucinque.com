+++
draft = false
slug = "appsec-reminder-sop"
share = true
date = "2017-01-29T13:35:24+11:00"
title = "An AppSec Reminder: SOP"
tags = ["security","tech", "english"]
image = "images/post-cover.jpg"
comments = true
+++
<br>

This post is part of a series that started [here](/post/appsec-reminder). This one specifically is about SOP, the cornerstone of Web security. <!--more-->

### SOP
The Same Origin Policy exists because we need to protect end-users, those accessing websites through browsers. _If SOP didn't exist, this is what could go wrong:_

**Scenario:**

Imagine Tom is logged in into his BestBank account on one tab, and has a dodgy-looking website open on a different tab.

BestBank trusts Tom's requests because when he logged in, the BestBank server gave him a cookie (`user_session=q847394872n7cwfisdhfsdhfls;`). So, as long as his requests have that cookie, BestBank is happy.

<img src="/images/csrf1.png" class="responsive-centered-image" />

**Attack:**

Caroline, the dodgy-looking website's owner, wants to rob $15 from Tom's account. All she needs is that cookie to impersonate him and make that request to BestBank as if she were Tom.

``` javascript
//pesudo-ish code.
http.post({
  url: 'https://bestbank.com/transfer',
  body: {
    from: 'tom-3234',
    to: 'caroline-the-cyber-thief-6644',
    amount: 15
  }
}).send();
```

If SOP didn't exist, BestBank would get the request and check that the cookie `user_session=q847394872n7cwfisdhfsdhfls;` is present.
And it would be indeed, because the **browser sends with every request to BestBank the cookies it has for BestBank's origin.**

```
POST /transfer/ HTTP/1.1
Host: bestbank.com
Referer: http://dodgy-looking-website.com
Cookie: user_session=q847394872n7cwfisdhfsdhfls;
```

This attack I just described is known as **[CSRF]**, or Cross Site Request Forgery.

**So, how does SOP help?**

**Browsers** (and browsers only!) will check the [origin] of the website making the request (`http://dodgy-looking-website.com`), and the origin where the request is going (`https://bestbank.com/transfer`). If they match, the browser will allow the request, if they don't match, then the browser will block it _(unless it is a [CORS] request)_.

<img src="/images/csrf2.png" class="responsive-centered-image" />

Single Origin Policy is one of the most important defences against attacks on internet. It allows websites to share the same browser while keeping the information segregated from each other.

ps: Browsers do allow requests (only `GET` requests) to a different origin from HTML tags such as `<script>`, `<img>`, `<form>`, `<link>`. But that's material for a different post!

ps2: SOP prevents a website on another tab to steal the cookie, but the attacker could still use other attacks to execute that Javascript but within the BestBank, which means the origins will match! That is call cross-site scripting or XSS and it is also material for a different post!

[origin]: http://CHANGEMEFORAREALREFERENCE
[CORS]: /post/appsec-reminder-cors
[CSRF]: /post/appsec-reminder-csrf
