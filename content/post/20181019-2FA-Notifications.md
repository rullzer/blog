---
title: "Two-Factor via Nextcloud Notifications"
date: "2018-10-19"
tags:
  - Nextcloud
  - Security
---

I'm happy to announce a new two-factor provider for your Nextcloud: the
*Notifications Provider*. This provider utilizes your existing logged in
devices to grant new devices access to your Nextcloud.

> Note: the app is still under heavy development. Still we appreciate testing
> and feedback!

The flow is simple. You enable the provider in your personal security settings.
Then the next time you log in you can chose to authenticate using a device that
is already logged in to your account.

{{% img-responsive "/static/images/posts/2FA-Notifications/login.png" %}}

Now a notification is dispatched. This is delivered to all your devices, which
means that you even get push notifications! That might just look something like:

{{% img-responsive "/static/images/posts/2FA-Notifications/android_push.png" %}}

You can approve or cancel the login from any of your devices. If you approved
the login then you will be automatically logged in.

You can grab the app from the [app store](https://apps.nextcloud.com/apps/twofactor_nextcloud_notification). And
your feedback, suggestions or pull requests are welcome on [github](https://github.com/nickv-nextcloud/twofactor_nextcloud_notification).
