---
title: "Strict-transport-security analysis"
date: "2022-10-13"
unlisted: true
tags:
  - STS
  - Security
---

I worked with the Strict Transport Security header when I was still at [Nextcloud](https://nextcloud.com). However my interest was reignited this week after
attending the [Practical TLS and PKI](https://www.feistyduck.com/training/practical-tls-and-pki) training by [Scott Helme](https://scotthelme.co.uk/). So I started
digging into the [crawler.ninja](https://crawler.ninja/) dataset to see what I could get out of it.

# What is the Strict Transport Security Header?
But first things first. For details read the [mdm docs](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Strict-Transport-Security). But in short it comes
down to a header a webserver can set on sercure requests to tell the client to only connect securely to that host for a given time. Lets jump right into an example:

``
Strict-Transport-Security: max-age=63072000; includeSubDomains; preload
``

This header will tell the client to connect only securely to this domain (and all its subdomains) for the next 63072000 seconds. So even if you would manually
navigate to http://DOMAIN.TLD the client would just rewrit this directly to https://DOMAIN.TLD

Pretty neat!

# Preloading
The header is pretty neat and will make sure in the future you connect only securely to your favorite websites. However, there is a tiny edge case. The first time
you access a website your request (can) still go over the big bad unsecured web. To solve this there is preloading. Anybody that meets the criteria can head over to
[hstspreload.org](https://hstspreload.org/) and submit their domain. If your request meets the criteria it will then be added to the big preload list that for example
chrome and firefox use. As the name suggest this list is preloaded so if you have never been to https://DOMAIN.TLD but DOMAIN.TLD is in the preload list. Your browser will
still always connect securely.

# The actual analysis
Now that we have the theory behind us. I wanted to see how the top 1 Million sites were doing. So I fired up a database imported the [crawler.ninja](https://crawler.ninja) dataset
of October 11th, grabbed the chrome and firefox preload lists and wrote some code.

The first thing I checked was how many of the top 1 million hosts are actually being preloaded. For both browsers this was less than 1% (8496 for Chrome and 8297 for Firefox).
I don't know what I was expecting but for sure I was expecting more than this. Especially given that there are 178212 domains preloaded in Chrome and 138393 domains preloaded in Firefox.
Which then also means that the fast majority of preloaded sites are not less often visited.

But it gets better. Of the 1M sites there are 26406 sites with a header that meets the preloading criteria. Now I understand it takes a bit of time to have your submission propagate. However similar numbers are observed in the dump from early June. If all those website would just go fill out the form we could tripple the number of preloaded sites of the most visited websites in the world in just a few weeks time.

# Understanding the header
Of all the sites in the dataset a total of 202781 have the header set in some shape or form. This is quite significant. Over 20% of the 1 million most visited sites on the web use this header. But that kind of raises more questions than it answers for me.

Upon further parsing the header the most often seen value for the `max-age` directive is to to 31536000 (365 days). Followed by half a year, 2 years etc. But then we see that there are also 7659
headers that set the max-age to 0. In total there are a little over 11 thousand (excluding 0) entries that have a max-age less of 1 hour or less. 
For sure such times are only used when debugging and making sure it all work. 
But we can take this a step further. 
A little over 20 thousand (excluding 0) entries have a max-age of 30 days or less. Meaning that they must have some kind of faith in their ability to keep their site securely accessible.

Of course there is also the other side of the spectrum. 1 site is being future proof by having a max-age of 1.576.800.031.536.000.

All in all this is not giving me a whole lot of confidence everybody knows what they are doing with this header.

# Is tis a problem?

One could argue this is not that much of a problem anymore. Chrome and Firefox are switching over to a HTTPS first approach. So preloading might become obsolete anyway. However we are not fully there yet. On top of that having a long list of sites your browser just knows never ever to connect to over an unsecure connection is still something I can only see as a good thing).

# The way forward?

So where do we go from here?:

* Ideally we start with getting all those sites that are ready for preloading into the overall preloaded list. But it is of course up to the owner of the domain to submit themselves.
* I would also argue that with the amount of crawlers out there we could find some way to speed up this preload process. Maybe if a host is seen multiple times in a momth with the requirements for preloading they could automatically be added?

# Acknowledgements
This is now the second time I use the [crawler.ninja](https://crawler.ninja) data for analysis, so again a huge thanks to Scott for running that. Also thanks to Scott for
acting as rubber duck when I was thinking trough all this and asking some valid questions on where to dig deeper.
