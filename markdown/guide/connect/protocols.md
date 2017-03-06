---
title: Communication Protocols
autotoc: true
nav_sort: 3
layout: guide.hbs
lunr: true
---

### Overview

This guide discusses the options for sending data to and from your cellular
device. In the table below, *Outbound* refers to sending data from the device,
and *Inbound* refers to outside data reaching the device.

{{{ table "communication-options" }}}

Per-MB data pricing and per-message SMS pricing depends on your zone and billing
plan. See [Pricing](/pricing/) for details.

### Hologram Cloud Messaging

The Hologram Cloud is the easiest way to send data between your device and the
internet. The mechanisms for inbound and outbound communication are somewhat different,
but in both cases, the Hologram cloud is responsible for forwarding messages
between the device and the internet using the device's cellular data.

#### Outbound (from Device)

The Dash provides built-in functions for sending messages to the Hologram Cloud.
Any connected device can use its [TCP API](/docs/reference/cloud/embedded), even 
if it's not on Hologram's cellular network. Messages take the form of a binary
payload (usually UTF8-encoded text), and an optional list of topic strings that
you can use for filtering or routing in the cloud.

By configuring routing rules in the Hologram Dashboard, you can forward messages
to other internet services. This means you can update where your
messages are sent without needing to deploy code updates to your
cellular devices. See the [Cloud Guide](/docs/guide/cloud/overview/) for
details.

#### Inbound (to Device)

Hologram exposes an API and a Dashboard interface to send TCP or UDP messages to
any port on your cellular device. This requires that your device runs embedded 
Linux or otherwise implements a networking stack. The Hologram Cloud
translates the API requests (or Dashboard submissions) into raw TCP/UDP messages
and sends them to the desired device.

See the [Cloud Messaging API reference](/docs/reference/cloud/http/#) for
details.

### Direct IP

Devices on the Hologram cellular network have access to the full internet, so
you may use any IP-based protocol to communicate with any internet-routable
host. In fact, Hologram's Cloud messaging API relies on this standard IP 
connectivity. Advanced users may wish to bypass the Hologram Cloud and instead
communicate directly with servers they control.

Hologram cellular devices do not have internet-routable IP addresses, so you can't initiate a
connection to your device directly from the internet. This means that to send
data *to* your device, the device must first establish a connection to an outside
server and request the data. The server can then transmit data back to the
device in a response using the already-established connection. This *request-response* 
pattern is the basis for many common protocols such as HTTP.

If device-originated requests aren't feasible for your application, you can
connect to your device via a tunnel, covered in the next section.

### SpaceBridge IP Tunnel

Since devices on the Hologram network do not have internet-routable IP
addresses, it's not possible to directly connect to the devices over the
internet. This makes it hard to use the device as a server that can listen for
incoming requests.
The solution is to establish a connection through a different server which does
have the ability to connect to your device. This is known as *tunneling*.

Hologram provides a service called SpaceBridge, which uses secure SSH tunneling
to let you connect to any port on your device. The inbound Cloud Messaging
feature above relies on a SpaceBridge tunnel behind the scenes. The Cloud
Messaging approach is a simpler alternative to SpaceBridge if you don't require
direct access to the network socket.

See our [SpaceBridge
guide](/docs/guide/cloud/spacebridge-tunnel/) for details.


### SMS via Hologram Cloud

#### Outbound (from Device)

To avoid the carrier fees associated with sending standard circuit-switched SMS
messages, Hologram offers a cloud API for sending SMS via TCP/IP. See *Send an
SMS via the Hologram Cloud* on the [Embedded cloud API reference
page](/docs/reference/cloud/embedded/).

Outbound SMS messages sent via API will appear as originating from an internal
Hologram phone number. This is the case even if you have purchased a phone
number for receiving inbound SMS messages.

#### Inbound (to Device)

Even if you don't purchase a phone number for your device, you can send SMS
messages to it from the Hologram dashboard or API. You may also configure the
phone number that the the SMS will appear to originate from.

From the Hologram dashboard page for your device, select the *Cloud & messaging*
view. Use the *Send an SMS message* form to send an SMS to your device:

{{{ image src="/wp-content/uploads/2016/11/dashboard-send-sms.png"
                   alt="Send SMS dashboard form" }}}

For information on sending SMS to the device via HTTP API, see the [Incoming SMS
API reference](https://hologram.io/docs/reference/cloud/http#/reference/hologram-cloud/sms/send-sms-to-a-device).

To read inbound SMSes on the device, you will likely need to use AT commands to
request them from the modem. Our [USB modem guide](/docs/guide/connect/usb-modem/) has
some general information about using AT commands, and [this external
article](http://www.smssolutions.net/tutorials/gsm/receivesmsat/) documents the
process for receiving SMS messages.

### Circuit-Switched SMS

#### Outbound

To send an SMS via the standard SMS switching network, you will likely need to
use AT commands with your cellular modem. Our [USB
modem guide](/docs/guide/connect/usb-modem/) describes the sequence required to send an
SMS.

Outbound SMS messages will appear as originating from an internal
Hologram phone number. This is the case even if you have purchased a phone
number for receiving inbound SMS messages.

#### Inbound

To receive SMS messages from other SMS-capable devices, you must purchase a
dedicated phone number for your Hologram device. This may be done from a
device page on the Hologram Dashboard, under *Cloud & messaging* > *Configure
phone number*.

To read inbound SMSes on the device, you will likely need to use AT commands to
request them from the modem. Our [USB modem guide](/docs/guide/connect/usb-modem/) has
some general information about using AT commands, and [this external
article](http://www.smssolutions.net/tutorials/gsm/receivesmsat/) documents the
process for receiving SMS messages.

