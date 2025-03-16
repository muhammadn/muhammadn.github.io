---
layout: default
title:  "Enabling getting native NMEA GPS data for Lenovo's Quectel RM520N-GL"
date:   2025-03-16 13:10 +0800
---

This is about a new laptop that i got, which is a customise Lenovo Carbon X1 Gen12 which includes a RM520N-GL 5G (sub-6Ghz) module by Quectel (but the device is specific to only Lenovo's version of RM520N-GL with a different device id)

Also Lenovo's Quectel RM512N-GL uses PCIe interface compared to the module you get directly from Quectel which uses the USB interface, so different drivers, heh.

Anyway, i will talk about the native Linux support with ModemManager (for Quectel's official client, that would be another post, folks!) - with PopOS! (22.04)

It works right out of the box with 5G connectivity, however, there is no NMEA support (the data from GPS satellites) and i felt it is a waste for having this feature in my hardware but i could not use it on my software.

So i decided to hack the Linux to get the NMEA GPS data out from the 5G modem.

It's not really that of a huge change but here's the patch:

```
diff --git a/drivers/net/wwan/mhi_wwan_ctrl.c b/drivers/net/wwan/mhi_wwan_ctrl.c
index e9f979d2d851..e13c0b078175 100644
--- a/drivers/net/wwan/mhi_wwan_ctrl.c
+++ b/drivers/net/wwan/mhi_wwan_ctrl.c
@@ -263,6 +263,7 @@ static const struct mhi_device_id mhi_wwan_ctrl_match_table[] = {
 	{ .chan = "QMI", .driver_data = WWAN_PORT_QMI },
 	{ .chan = "DIAG", .driver_data = WWAN_PORT_QCDM },
 	{ .chan = "FIREHOSE", .driver_data = WWAN_PORT_FIREHOSE },
+	{ .chan = "NMEA", .driver_data = WWAN_PORT_NMEA },
 	{},
 };
 MODULE_DEVICE_TABLE(mhi, mhi_wwan_ctrl_match_table);
diff --git a/drivers/net/wwan/wwan_core.c b/drivers/net/wwan/wwan_core.c
index a51e2755991a..ebf574f2b126 100644
--- a/drivers/net/wwan/wwan_core.c
+++ b/drivers/net/wwan/wwan_core.c
@@ -342,6 +342,10 @@ static const struct {
 		.name = "MIPC",
 		.devsuf = "mipc",
 	},
+	[WWAN_PORT_NMEA] = {
+		.name = "NMEA",
+		.devsuf = "nmea",
+	},
 };
 
 static ssize_t type_show(struct device *dev, struct device_attribute *attr,
diff --git a/include/linux/wwan.h b/include/linux/wwan.h
index a4d6cc0c9f68..ca38a2fe0987 100644
--- a/include/linux/wwan.h
+++ b/include/linux/wwan.h
@@ -19,6 +19,7 @@
  * @WWAN_PORT_FASTBOOT: Fastboot protocol control
  * @WWAN_PORT_ADB: ADB protocol control
  * @WWAN_PORT_MIPC: MTK MIPC diagnostic interface
+ * @WWAN_PORT_NMEA: NMEA GPS statements interface
  *
  * @WWAN_PORT_MAX: Highest supported port types
  * @WWAN_PORT_UNKNOWN: Special value to indicate an unknown port type
@@ -34,6 +35,7 @@ enum wwan_port_type {
 	WWAN_PORT_FASTBOOT,
 	WWAN_PORT_ADB,
 	WWAN_PORT_MIPC,
+	WWAN_PORT_NMEA,
 
 	/* Add new port types above this line */

```

or download the patch file from [here]({{ site.url }}/assets/files/quectel-lenovo-rm512n-gl.patch).
