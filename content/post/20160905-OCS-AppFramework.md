---
title: "OCS Supported by the AppFramework"
date: "2016-09-05"
tags:
  - Nextcloud
  - AppFramework
  - OCS
---

Nextcloud exposes some APIs to the outside works over HTTP. There is of
cou rse our [webdav] endpoint. That, among other things, allows you to
retrieve and store your files or update your calendar. Probably our
second most used endpoint is the [OCS Share API]. This is used by a lot
of clients that connect to your Nextcloud to share files. As the name
suggest this is an OCS (Open-Collaboration-Services) API of which we
have a few.

The OCS API has been around for quite some time now. And it was in need
for some love. Which is why I am glad to announce that starting from
Nextcloud 11 you can build OCS API endpoints using our AppFramework.

This brings a lot of improvements. Some examples:

-   Automatic dependency injection: This makes writing code much more
    fun. Your Controllers just specify that is required and most of the
    time the AppFramework will figure out how and where to get the
    dependencies.
-   Security by default: By default, a lot of security hardenings are in
    place. You can opt out of some (like for public routes). But this
    makes it harder to mess up.
-   No more static classes: This will make it easier and faster to test
    your classes.

And of course this also means your API will directly benefit from
enhancements we make in the AppFramework.

We will of course still support the current approach to create OCS API
endpoints for the foreseeable future. Feel free to reach out to us on
[help.nextcloud.com] if you need help.

  [webdav]: https://en.wikipedia.org/wiki/WebDAV
  [OCS Share API]: https://docs.nextcloud.com/server/10/developer_manual/core/ocs-share-api.html
  [help.nextcloud.com]: https://help.nextcloud.com/c/dev/app-dev
