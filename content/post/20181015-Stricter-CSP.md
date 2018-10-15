---
title: "Towards a stricter Content Security Policy"
date: "2018-10-18"
tags:
  - Nextcloud
  - Security
---

A Content Security Policy (*CSP*) can be used to protect against Cross Site Scripting (*XSS*) attacks. This is done by having the server tell the browser what resources (executable script, images, etc) can be loaded from where. All this is told to the browser via a header, so if the actual page tries to do something it is not allowed the browser will block it.

At Nextcloud we have deployed a *CSP* for a while now that limited the resources to be loaded mainly to the domain your Nextcloud is running on. So by default it is not possible to load a random script from somewhere on the web.

We also added a nonce so that only javascript with the proper nonce set would even get execute by (modern) browsers.

Now because of the way a lot of our javascript code and a lot of 3rdparty javascript code was written we did allow **unsafe-eval**. Which allowed the execution of **eval**. This function is probably best known for being a well known *XSS* attack vector.

# Dropping unsafe-eval

Of course I would be writing this if we did not change anything about this behavior. Which brings me to Nextcloud 15 where we will disallow **unsafe-eval** by default in our *CSP*. This means that your Nextcloud will, by default, not permit execution of **eval**. Which results in a safer Nextcloud.

## Information for developers

We have tried to make the transition to a stricter *CSP* as smooth as possible. However it could be that if you have written a Nextcloud app that you need to take action.

We are actively checking applications if they still work as expected and submitting pull requests. But of course help is appreciated. The easiest way to verify is to download the latest [Nextcloud daily](https://download.nextcloud.com/server/daily/latest-master.zip) and try out your app. Be sure to have your developer tools open and keep an eye on the console. If the *CSP* is violated a message will be shown there.

If you need help feel free to mention me (*@rullzer*) on github or drop by in [#nextcloud-dev on Freenode](https://webchat.freenode.net/).
