---
layout: default
title:  "Patching and Compiling SAMBA on macOS Mojave"
date:   2019-11-19 19:24:15 +0800
---

Latest versions of macOS does not include SAMBA but has their own tool called `smbutil` that was imported from FreeBSD

Even brew (yeah, the package manager) did not want to maintain SAMBA in the package tree because it is a hell to compile SAMBA on macOS.

But because i wanted to message my wife on her computer (she works in a cybercafe) with Windows messaging popups so i decided to hack SAMBA source and make it compile and run on macOS.

The most interesting find is even the latest macOS is using the rather old _editline_ instead of the more common _GNU readline_, thanks to the information i got from this the blog [HERE](https://blueslugs.com/blog/2016-10-23-updating-cppfunction-in-old-readline-consumers/)

These are the instruction for samba-4.11.2 on macOS Mojave:

1. Download samba-4.11.2 from [https://samba.org](https://samba.org)
2. Download the patch file: [samba-4.11.2-readline.patch]({{ site.url }}/assets/files/samba-4.11.2-readline.patch)
3. Uncompress the samba-4.11.2 source file
4. Copy the patch file to the root samba-4.11.2 directory
5. run `patch -p0 < samba-4.11.2-readline.patch` in samba-4.11.2 root directory
6. Lastly, run `./configure --without-ad-dc --disable-python --without-libarchive --without-acl-support && make && make install`
7. Enjoy! 🙌
