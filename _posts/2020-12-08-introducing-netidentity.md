---
layout: default
title:  "Introducing NetIdentity"
date:   2020-12-08 14:16:00 +0800
---

![NetIdentity]({{ site.url }}/assets/images/netidentity-diagram.png){:class="img-responsive"}

I started doing this when i found out that most API Providers are too costly or has high latency (hello ip2location!) and i needed this for [Awesell](https://awesell.com) and [Awesell](https://awesell.com) has now been integrated with [NetIdentity](https://netidentity.io). 

So it was actually designed to solve my own problem and looking at the potential NetIdentity would be for others, i decided to build it for low latency and high availibility.

You can actually use it now on [Rakuten's RapidAPI](https://english.api.rakuten.net/netidentity-netidentity-default/api/netidentity1/endpoints)  (we have still to work on our [site](https://netidentity.ui))

The actual interesting part here is the CDN Edges running my serverless code to generate the data and make a call to origin server ONLY when necessary.

This serverless code runs directly on the CDN Edge servers closest to the user location and backed by a key-value storage distributed across the CDN Edges (think Japan, EU, US, SG.. etc)

There are two tiers here in the picture - User gets the data via Rakuten RapidAPI or directly to our CDN Edge.

For Rakuten RapidAPI latency is 130-150ms (it's still good!, ~200 is already bad) because it has to do an additional trip to the CDN. For Example: (User -> Rakuten US -> CDN SG)

But i will recommend using our CDN directly for our future users to get less than 100ms (about 38-80ms)
