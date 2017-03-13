---
title: Sending Messages to a Device
nav_sort: 3
autotoc: true
layout: guide.hbs
lunr: true
tags:
---

The Hologram Cloud provides an API and a Dashboard interface to send TCP or UDP 
messages to any port on a cellular device. By running a server on your
device that listens on a given port, you can receive and process
these messages.

{{#callout}}
The Hologram Dash does not support receiving cloud messages. You must use SMS
to send data to the Dash.
{{/callout}}

### Start a test server

One simple way to start a test TCP server on a Linux device (e.g. Raspberry Pi) is
with the `netcat` command. To listen on port 9876 and print any incoming message,
run the command:

```bash
nc -l -p 9876
```

By default `netcat` will quit after the sender closes the connection. Some
versions support a `-k` option to keep listening indefinitely.

A more realistic application might use a socket library in a
language like Python, where you could then parse and act on the
incoming messages. But for illustrating the communication channel itself, `netcat`
is nice and simple.

### Send a message

With your server listening on the cellular device, you have several options for
sending a cloud message.

#### From the Dashboard

You may send messages to a device from the device's page on the [Hologram
Dashboard](https://dashboard.hologram.io/). Open the *Messaging* section and
complete the *Device Messaging* form, making sure to select "Cloud Data" as the
message type:

{{{ image src="/wp-content/uploads/2017/01/cloud-data-form.png" 
    alt="Cloud messaging form" }}}

Enter the same port number as you configured your server to listen on, and
select TCP as the protocol. When you submit the form, the Hologram cloud will
forward the message to your device.

#### Via HTTP API

Messages can be sent via HTTP API using the [`/devices/message`
endpoint](/docs/reference/cloud/http/#/reference/hologram-cloud/cloud-to-device-messaging/send-message-to-a-device/).

#### Via Inbound Webhook URL

The HTTP API above requires an API key to authenticate. This can make it
difficult to integrate with an external service that simply sends webhook
requests to a configurable URL. To accommodate this use case, you may generate a
special URL endpoint which does not require additional authentication.

To generate a URL for inbound webhooks, open the *Cloud Configuration* section 
on a device's dashboard page. Click *Generate webhook* and specify which port
and protocol (TCP or UDP) to forward data to:

{{{ image src="/wp-content/uploads/2017/01/webhook-url-form.png" 
    alt="Inbound webhook configuration" }}}

The request body must contain a 'data' field with a string value. This value gets 
emitted to the device on the configured port.

### Replies

If the device sends data back through the socket connection after reciving a
message, it gets sent as a message to the [Cloud Services
Router](/docs/guide/cloud/csr/).

### Advanced messaging with Spacebridge

Spacebridge is a lower-level alternative to cloud messaging. It acts as a secure
proxy from a device's port to a port on your local machine. This allows you to
implement other two-way TCP- and UDP-based protocols to communicate with your device.

See our [Spacebridge guide](/docs/guide/cloud/spacebridge-tunnel/) for details.

