---
title: Connect Your Device
nav_sort: 0
autotoc: true
layout: guide.hbs
lunr: true
tags:
---

The Hologram network is device-agnostic. All you need to get online
is an activated Hologram Global SIM Card and a cellular device or modem.

We sell USB modems on [our online store](/store) that can be used with any
computer or Linux-based embedded device. See our 
[USB modem guide](/docs/guide/connect/usb-modem/) for details.

{{#callout title="Dash-specific guide"}}
Connecting with the Hologram Dash board? Check out our
[Dash Quick Start guide](/docs/guide/dash/quick-start) for more
comprehensive instructions.
{{/callout}}

### SIM Activation

{{{ include "activate-sim" }}}

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

### Next steps

Now that your device is connected, you may want to explore options for 
[communication protocols](/docs/guide/connect/protocols/) to send data to and
from your device.

Our [Cloud Services Router](/docs/guide/cloud/csr/) is a great option for
routing data from your device to other internet services.


