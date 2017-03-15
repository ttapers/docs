---
title: Billing and Usage
nav_sort: 0
autotoc: true
layout: guide.hbs
lunr: true
tags:
---

### Billing Period

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

### Usage Data

The [usage page](https://dashboard.hologram.io/devices/usages) in the dashboard
contains several reports and visualizations of your device activity:

{{{ image src="/wp-content/uploads/2017/01/Screen-Shot-2017-01-05-at-2.55.53-PM.png"
    alt="Usage page" }}}

From this page, you can view data usage on a per-device basis as well as
aggregated across your account. You can also download CSV usage reports.

### Automatic Refill

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

### Data and Overage Limits

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

You can also apply overage limits to many devices at once from the device list
page. Select which devices to apply the limit to, and select *Manage*->*Set data
limits/overage*. Note that the limits are applied per-device, not on an
aggregate of usage.

{{{ image src="/wp-content/uploads/2017/03/bulk-device-actions.png"
    alt="Bulk device actions" }}}

### Pausing Data

Pausing a device's data allows you to temporarily disable all connectivity for
the device. This means the corresponding SIM will not be able to send or receive
SMS, or connect to the internet. This may be useful if you expect a device to be
inactive for a period of time and wish to ensure that no usage charges accrue.

While a device is in a paused state, monthly plan charges still apply. When
pausing a device for an extended period, you would typically first convert it to
a pay-as-you-go plan, so that the paused device will only be charged the low
monthly SIM base cost.

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

