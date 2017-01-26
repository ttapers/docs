---
title: Sending Messages to a Device
nav_sort: 3
autotoc: true
layout: guide.hbs
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

By default `netcat` will quit after the sender closes the connection, but some
versions support a `-k` option to keep listening indefinitely.

A more realistic application might use a socket library in a
language like Python, where you could then parse and act on the
incoming messages. But for illustrating the communication channel itself, `netcat`
is nice and simple.

With your server listening on the cellular device, you have several options for
sending a cloud message:

### Send from the Dashboard

You may send messages to a device from the device's page on the [Hologram
Dashboard](https://dashboard.hologram.io/). Open the *Messaging* section and
complete the *Device Messaging* form, making sure to select "Cloud Data" as the
message type:

{{{ image src="placeholder-replace-me.jpg" alt="Cloud messaging form" }}}

Enter the same port number as you configured your server to listen on, and
select TCP as the protocol. When you submit the form, the Hologram cloud will
forward the message to your device.

### Send via HTTP API

Messages can also be sent via HTTP API using the [`/devices/message`
endpoint](/docs/reference/cloud/http/#).


### Send via Inbound Webhook URL

The HTTP API above requires an API key to authenticate. This can make it
difficult to integrate with an external service that simply sends webhook
requests to a configurable URL. To
accommodate this, you may

### Spacebridge


### Replies

