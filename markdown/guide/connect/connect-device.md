---
title: Connect Your Device
nav_sort: 0
autotoc: true
layout: guide.hbs
lunr: true
---

The Hologram network is device-agnostic. All you need to get online
is an activated Hologram Global SIM Card and a cellular device or modem.

We sell USB modems on [our online store](/store) that can be used with any
computer or Linux-based embedded device. See our 
[USB modem guide](/docs/guide/connect/usb-modem/) for details.

### SIM Activation

Activate your Hologram Global 
SIM Card on [our Device Dashboard](https://dashboard.hologram.io) or via 
the [HTTP API](/docs/reference/cloud/http). The SIM must be a Hologram-branded 
card that has not previously been activated.

When activating a SIM, you must choose a data plan and geographic zone; see our
[pricing page](https://hologram.io/pricing/) for details.
Different devices can have different plans or zones. You can change the plan
of an existing device by contacting Hologram support from the dashboard.

### APN Settings

With your device **powered off**, insert the Hologram Global SIM
Card into your device and power your device back on.

Then, all you need to do is configure your device to use Hologram's
Access Point Name (APN) to load the proper configuration.
Consult your device's documentation on how to configure the 
APN.

The correct APN settings are as follows:

* **APN:** `hologram`
* **APN username:** *(none)*
* **APN password:** *(none)*
* **IP Address:** *Dynamic (using DHCP)*
* **Data Roaming:** *Enabled*

That's it! Your device should now be connected to the Hologram cellular network.

### Device Phone Numbers

A phone number allows you to easily send an SMS to your Hologram-connected device 
from any SMS-compatible device. Phone numbers are not necessary to deliver an SMS to your
device via the Dashboard or API.

You may purchase a phone number for a device from the device's page on the
Hologram Dashboard. The monthly cost for the number depends on the number's
country code.

Note that the device will be able to respond to the SMS, but the response
may show up as originating from a different number. This is a special, internal number
so you should be sure to send all messages to the purchased phone number instead
of the internal number.

