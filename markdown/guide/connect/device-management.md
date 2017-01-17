---
title: Device Management
nav_sort: 1
autotoc: true
layout: guide.hbs
---

### Overview

Hologram associates SIM cards, data plans, authentication, and various other
data through a common *device* object. A device could be a Hologram Dash, a PC,
or some custom hardware. 

Most of a device's attributes are related to its connectivity to the Hologram
network, but you can even create devices without cellular connectivity--for
instance, to generate source credentials for the Hologram Cloud.

This guide describes the most important properties of a device. Most of these
properties are visible and editable via the [Hologram
Dashboard](https://dashboard.hologram.io/). When noted, specific features
may only be accessible through the [HTTP API](/docs/reference/cloud/http/).

### SIM Activation

In the Hologram Dashboard, a device is created by activating a SIM card to be
associated with the device. This must be a Hologram-branded SIM that has not
previously been activated.

When activating a SIM, you must choose a data plan and geographic zone; see our
[pricing page](https://hologram.io/pricing/) for details.
Different devices can have different plans or zones. You can change the plan
of an existing device by contacting Hologram support from the dashboard.

### Usage data

The [usage page](https://dashboard.hologram.io/devices/usages) in the dashboard
contains several reports and visualizations of your device activity:

{{{ image src="/wp-content/uploads/2017/01/Screen-Shot-2017-01-05-at-2.55.53-PM.png"
    alt="Usage page" }}}

From this page, you can view data usage on a per-device basis as well as
aggregated across your account. You can also download CSV usage reports.

### Billing

The billing period for a data plan is 30 days, beginning from the time of
activation. At the end of the billing period, the device's plan amount will be 
deducted from your account balance. 

As long as there is a remaining balance in your account, the device's service
will automatically renew at the end of a billing period. If your account balance
cannot cover the cost of renewing a device's data plan, you will receive an
email notification 24 hours before the end of the billing period.

For pay-as-you-go plans and data in excess of plan allowances, the usage charges
are deducted in real time as the data is used. In this case, if your account
balance reaches zero, your data plan may be paused without warning. To prevent
this from happening, we suggest configuring Automatic Refill.

#### Automatic Refill

Hologram’s Automatic Refill feature allows users to add an amount that will be
charged to a credit card on file whenever the account balance gets
below a minimum amount designated by the user. Though Hologram does not
require users to enable this feature, it is strongly suggested to avoid service
interruption.

To enable Automatic Refill, navigate to the 
[Billing](https://dashboard.hologram.io/account/billing) page on the dashboard.
Make sure you have a valid payment method on file under *Billing Overview*,
then check the *Enable Auto-Refill* checkbox. You can then set the refill amount and
threshold.

#### Data and Overage Limits

You can limit your usage charges by configuring a limit to how much data a
device can consume during a billing period.

* **Monthly plans**: an overage limit defines how much data can be used *after*
  the monthly allowance has been exhausted.
* **Pay-as-you-go plans**: a data limit defines how much overall data can be
  used during a billing period.

For either type of plan, the limit can be configured as a number of bytes per
month. Change the data limit for a device from the *Plan and Coverage* view
on the device's dashboard page:

{{{ image src="/wp-content/uploads/2016/11/device-data-limits.png"
                   alt="Device data limits configuration" }}}

#### Pausing Data

Pausing a device's data allows you to temporarily disable all connectivity for
the device. This means the corresponding SIM will not be able to send or receive
SMS, or connect to the internet. This may be useful if you expect a device to be
inactive for a period of time and wish to ensure that no usage charges accrue.

While a device is in a paused state, monthly plan charges still apply. When
pausing a device for an extended period, you would typically first convert it to
a pay-as-you-go plan, so that the paused device will only be charged the low
monthly SIM base cost. 

### Hologram Cloud Credentials

When sending a message to Hologram's Cloud Services Router (CSR) via the [TCP
API](/docs/reference/cloud/embedded/), you must provide authentication
credentials to indicate which device was the source of the message. The
credentials consist of a 4-byte *Source ID* and a 4-byte *Key*. You may view
these credentials under *Cloud & messaging* on the device dashboard page. 

If you believe that a device's credentials may have been compromised, you can
regenerate them. Each device only has one active set of credentials at a time,
so the old credentials will no longer authenticate successfully after
regenerating.

{{#callout}}
The Hologram Dash uses a separate authentication method when sending CSR
messages. It is therefore not necessary to generate CSR credentials for the
Dash, and regenerating credentials will not impact the Dash's ability to send
messages.
{{/callout}}

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


