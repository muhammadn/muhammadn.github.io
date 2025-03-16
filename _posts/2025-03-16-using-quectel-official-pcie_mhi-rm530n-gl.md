---
layout: default
title:  "Using Quectel's official mhi_pcie driver for Lenovo's Quectel RM520N-GL"
date:   2025-03-16 13:35 +0800
---

Ok, this would be short. This is an official driver for Lenovo Quectel RM520N-GL if you don't want to use `ModemManager`.

I think this solution is simple but i felt i wanted more control but anyways, i've tested this out and it works! Just that it's a shame to keep it to myself so i'm posting it here.

Apparently Quectel has different versions of RM520N-GL 5G module. The ones sold by Quectel normally connects via USB interface, whereas the Quectel RM520N-GL is a PCIe interface.

So i'm going to talk about the official mhi_pcie driver from Quectel which i had modified to support Lenovo variant of this same model.

You can download mhi_pcie driver [here]({{ site.url }}/assets/files/Quectel_Linux_PCIE_MHI_Driver_V1.3.8.zip) 

Then apply this patch i wrote [here]({{ site.url }}/assets/files/lenovo-rm520n-gl.patch)
