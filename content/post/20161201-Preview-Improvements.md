---
title: "Nextcloud 11 Preview Improvements"
date: "2016-12-12"
tags:
  - Nextcloud
  - Previews
---

If you store images on your Nextcloud there is a big change that you
have previews enabled. Previews are used for the tiny thumbnails in the
file list but also for scaled down images in gallery for example.
Because nobody wants transfer their 30 mega pixel photos all the time.

In Nextcloud 11 we have several nice improvements for you regarding
previews. Including an shiny new app to pre-generate previews!

Serving previews
----------------

First of all we changed the way previews are served to the end user. If
a preview was generated we would first construct an image object in
memory and then serve the data from that image to the user. This created
a lot of overhead.

So the first step is skipping the image parsing in memory. If a preview
is created once we just serve the data from that file. This saves
precious RAM and CPU cycles.

Etag validation
---------------

By default we cache previews for 24 hours. However, after those 24 hours
we would just request the same preview object again. While for a
majority of the preview files nothing would have changed at all.

We now actually look at the headers a client sends us and if it is the
same file (etag/last modified date). We just return the good old 304
status code. To indicate nothing has changed. We can do this by just
accessing the database. So no need to go to the slow storage unless we
have to actually serve preview data.

Limiting preview sizes
----------------------

Let say you request a preview of an image of 250 by 250. But the next
time you request an image of 252 by 252. Now it makes no sense to
generate a new preview. We should serve similar preview sizes as the
same file. There was already some code that handled this but we upgraded
it in Nextcloud 11.

The algorithm we use now rounds your image up to the nearest power of 2.
So you get at least an image of the size you requested but it might be a
bit bigger. But your browser is also very good at resizing it a bit for
you then. This makes sure that your disk does not fill up with 10K
previews per file and that the server can often just serve the same
file.

Shared previews
---------------

Up until now we would generate previews per user. So if Alice shared a
folder full of their holiday pictures with Bob we could generate
previews for both Alice and Bob. This separation lead to multiple
issues:

-   Double the space required
-   Double the CPU cycles for preview generation
-   If Bob modified a file the preview of Alice would not get updated
-   When Bob opens the folder all the previews still have to be
    generated which requires him to wait until he can browse them

As of Nextcloud 11 we share previews. Which means that we have a single
location to store previews. This solves the problems Alice and Bob had
as described above.

Pre-generation
--------------

The downside of the new preview approach is that we need to drop all the
existing previews. Because of certain bugs in the old preview system
just moving stuff over is not possible.

To over come this we have been working on an app. This app allows you to
do a one time run to generate all the previews.

However the app is capable of doing more. It will listen to writes and
keep a list of all those files. Then once you run a command it will
start requesting previews for all those files. This can make your
overall experience more smooth.

That way you can lets your server generate previews during the night for
example.

Get the app in the [appstore].

  [appstore]: https://apps.nextcloud.com/apps/previewgenerator
