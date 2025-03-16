---
layout: default
title:  "Refusing DNS queries in systemd-resolved"
date:   2025-03-16 13:44 +0800
---

For DNS at our current implementation of resolvers, the DNS resolvers will try to resolve IPv6 records (query `AAAA`) even when IPv6 stack has been disabled in the Linux kernel.

We had tried to use `no-aaaa` but this is just a glibc *hack* that the glibc maintainers use to test applications and not for **production**

So i took the [initiative](https://github.com/systemd/systemd/pull/34165) to actually implement a feature to refuse `AAAA` IPv6 DNS queries outright from systemd-resolved. This feature will be shipped with systemd-v258.

After much discussion with the maintainers of systemd-resolved and particularly Leonart Poettering who suggested having an option to also refuse other types of queries, which *can* be `A` or `SRV` or `TXT`.

The nice side effect is that we can lockdown on what the servers are supposed to query. My initial idea was for `AAAA` records but we can now refuse ActiveDirectory records (`SRV` and `TXT`) which can secure the servers in case of network probing when there is an intrusion.

So now in `resolved.conf` you can set `RefusedRecordTypes=AAAA SRV TXT` to refuse quering those record types.
