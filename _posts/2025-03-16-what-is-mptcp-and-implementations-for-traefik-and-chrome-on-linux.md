---
layout: default
title:  "What is MPTCP and implementations for Traefik and Chrome on Linux"
date:   2025-03-16 02:07 +0800
---
As being the usual me, i would triage the Linux kernel CHANGELOG for any updates with regards to my Lenovo ThinkPad Carbon X1 hardware.

I had read about MPTCP a few months ago but after reading the MPTCP improvements to the Linux kernel, i thought of working on getting more application support for Linux.

With MPTCP, you can combine all your network interface bandwidth in a single (MP)TCP connection via subflows.

I've benchmarked it over my GigE (1Gbps) and WiFi (200Mbps max) on LAN.

Without MPTCP it could go up to 920Mbps on GigE and with MPTCP it will aggregate to 1.30Gbps (1300Mbps) as it MPTCP combines both GigE 1Gbps and my WiFi 300Mbps.

That's over LAN and not over internet. For internet, i have 5G on my laptop so it combines with around 600Mbps and my fiber is around 300Mbps which i can get an aggregate of 900Mbps.

What's good about MPTCP is that i don't see disconnections (restarted my core fiber router and my laptop does not get interrupted)

The caveat is that the server must also support MPTCP.

I've contributed a small change for [traefik reverse proxy](https://github.com/traefik/traefik/pull/11601) for server.

Also added native MPTCP client support for [Chrome on Linux](https://chromium-review.googlesource.com/c/chromium/src/+/6355767) so you won't need an `mptcpize` LD_PRELOAD *shim* to force-enable MPTCP to official Chrome version.

First time working on C++ codebase but it's quite easy as i had worked on C, so it's pretty natural.

Let's see how it goes, surely it will be a win-win for getting a reliable connection and having a speed bump when using Chrome on Linux.
