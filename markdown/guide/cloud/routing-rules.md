---
title: CSR Routes
nav_sort: 1
autotoc: true
layout: guide.hbs
---

### Overview

In order to forward messages from the Cloud Services Router (CSR)
to your desired destination, you must configure a route.
Possible destinations include Email or SMS, web apps such
as Slack, or any internet-connected service via webhook.

Routes can be configured through the 
[Hologram Dashboard](https://dashboard.hologram.io/routes) or the
HTTP API.

#### Components of a route

An route configuration has two parts: the message topic(s) that the route
subscribes to,
and the destination where the applicable messages get published.

A route subscribes to messages with *any* of the specified topics.  In other
words, a route subscribes to the union (*OR*) of its list of topics.

The CSR supports several route types and formats:

* **Slack** -- Send payload as a Slack message
* **SMS** -- Send payload as a text message
* **Email** -- Send payload and metadata in an email
* **AT&T M2X** -- Publish data to AT&T's M2X platform
* **Heartbeat** -- Emit an event if no data has been received for a period of
  time
* **Advanced Webhook Builder (Your Own App)** -- Send a configurable HTTP request
  to a URL
* **Custom Webhook URL (Your Own App)** -- Send payload and metadata in
  a predefined JSON format to a URL

{{{ image src="/wp-content/uploads/2016/11/route-create-form.png" alt="Route create form" }}}


### Slack

Slack is a group messaging application that has great support for integrating
with external services. Hologram's Slack integration posts matching messages
to a configurable Slack channel.

First, generate a Slack webhook URL by visiting the [Incoming
Webhooks](https://my.slack.com/services/new/incoming-webhook/) page. Make sure
you're signed into the correct Slack team, then pick which channel to route
Hologram messages to. Click the *Add Incoming Webhooks integration* button to
generate a new integration URL. From this page you can also change the icon and
name that are displayed when Hologram posts a message.

Create a Hologram route, and select *Slack* as the route type.
The Slack route has two parameters:

* **Slack Webhook URL:** The URL you generated above. It should
  begin with *https://hooks.slack.com/*.
* **Slack Channel:** Optionally override the channel to post to. If left blank,
  the channel will be determined by the Incoming Webhook configuration.

Hologram messages will display as two lines in Slack: the first line is the
message payload in text format, the second line is a JSON representation of the
message, including metadata such as received time and message topics.

### AT&T M2X

M2X is a platform by AT&T for logging and streaming data from
device sources to various cloud destinations. M2X organizes data within *devices*, 
where each device can have multiple *streams* of numerical or text data.
Hologram's M2X integration forwards messages from Hologram devices to a stream
on a corresponding M2X device, creating the device if necessary.

In order for Hologram to authenticate to your M2X account, you must provide an
M2X API master key.  To find your master key, access your [account
settings](https://m2x.att.com/account) by clicking on your account name at the
top-right, then selecting *Account Settings* from the dropdown menu.  Copy the
automatically created master key from this page:

{{{ image src="/wp-content/uploads/2016/10/m2x-master-key.png"
  alt="M2X master key" }}}

{{#callout}}
Even if you only plan to write to one M2X device, Hologram cannot use a
device-specific API key because it needs to query for whether a device already
exists.
{{/callout}}

Create a Hologram route, and select *AT&T M2X* as the route type.
The M2X route has two parameters:

* **API Key:** The master API key found on the M2X account settings page
* **Stream Name:** The name of the stream to publish the message to. Default is
  `konekt_default`.

#### Device and Stream Mapping

Hologram relies on M2X's Device Serial identifier for mapping between Hologram and M2X
devices.
Messages originating from a Hologram device with ID `12345` will be routed to
the M2X device with serial `konekt_12345`. If a device with this serial doesn't
exist, it will be automatically created. 

If the destination device doesn't have a stream with the given stream name, a
non-numeric stream will be created. This allows for arbitrary text payloads to
be stored in M2X. However, if you pre-create a numeric stream with the given
name, M2X will attempt to parse the data payload as a number. This can be useful
for devices sending numeric sensor readings to the Hologram Cloud.

### Heartbeat

The Heartbeat routing rule emits an event if it does not receive data from its
subscribed topics for a period of time. This can be useful to generate alerts
when devices unexpectedly go offline.

While most routing rules send their output to an external service, the Heartbeat 
rule feeds back into the CSR under a different, configurable topic. By creating another rule that
subscribes to the Heartbeat output, you can send the alert to any external
destination. For example, let's create an alert to send an email when the device
with ID `12345` fails to write a message for 15 minutes.

Create a Hologram route, and select *Heartbeat* as the route type. The heartbeat
rule has two parameters:

* **Publish To:** The CSR topic that this rule will write an event to. This
  should be different than the *Subscribes to* topic.
* **Heartbeat time:** The output event will be sent if no messages are received
  for this many seconds.

{{{ image src="/wp-content/uploads/2017/01/heartbeat-route.png"
          alt="Configuration for Heartbeat route" }}}

Then, create an email route which subscribes to the *Publish To*
topic that you defined in the Heartbeat route. Now, if your device
fails to write a message in a 15-minute period, you'll receive an email.


### Custom Integrations With Webhooks

If you need to send messages to an app or service that Hologram doesn't
natively support, you may use one of the Webhook route types to generate an
HTTP POST request to any URL. This is especially useful for integrating with
your own custom web app.

{{#callout title="Tip"}}
To verify the contents and format of webhooks,
it can be useful to send them to an HTTP
request inspector such as [RequestBin](https://requestb.in/).
Use your generated RequestBin URL as the destination URL in
the route config, then refresh the RequestBin inspector page
after you send a message to the CSR. It should then display
the HTTP headers and content of the corresponding webhook.
{{/callout}}

#### Custom Webhook URL

The *Custom Webhook URL* destination sends a *form-urlencoded* HTTP POST 
request with a message's 
data payload and metadata in a pre-defined format. 
The request includes a `payload` field which contains a JSON
representation of the message. The payload is formatted as follows:

```json
{
  "received": "2016-09-27T18:53:09.302915",
  "authtype": "psk",
  "tags": ["TOPIC1", "_DEVICE_12345_"],
  "device_name": "My Device",
  "errorcode": 0,
  "source": "abcd",
  "timestamp": "0",
  "data": "SGVsbG8sIHdvcmxkIQo=",
  "device_id": 12345
}
```

The `data` field contains a base64-encoded representation of the data payload.

When developing an application that accepts webhooks, it's
important to have a reliable way to check that an HTTP request is coming from
the intended source. The *Custom Webhook URL* destination lets you specify a
key that is included in every webhook that gets sent. Then, your application
can easily check for the presence of this key to ensure that the request
indeed originated from Hologram. This string is included as a field
called `key` alongside the `payload` in the request.

See the [CSR Reference](/docs/reference/cloud/csr/) for details about
the data fields sent in messages from the CSR.

#### Advanced Webhook Builder

The *Advanced Webhook Builder* sends HTTP POST requests where the body
and URL can be customized via templates. It is therefore more flexible
than the *Custom Webhook URL* destination--it can be used when the
destination application expects an HTTP request in a specific format.

Variables can be included by enclosing the variable name in double angle
brackets, e.g. `<<device_id>>`. Supported variables are:

* `received`: ISO8601-formatted UTC timestamp when the message was received by
the CSR
* `tags`: JSON-formatted array of topics belonging to the message
* `device_name`: Human-readable name of the device
* `device_id`: Integer ID of the device
* `data`: Base64-encoded representation of the data payload
* `decdata`: Decoded (raw) representation of the data payload. This may not be valid
  if the message has a non-text payload.
* `decdata.fieldName`: Access a JSON field in the data payload, for example,
  `decdata.voltage`. Results in an empty string if the payload isn't valid JSON
  or if the field isn't present.

One common use case is to construct a custom JSON string as the webhook
body. For example, the payload template could be:

```json
{
  "text_data": "<<decdata>>",
  "source_device": "<<device_id>>",
  "datetime": "<<received>>"
}
```

Which would result in a webhook body such as:

```json
{
  "text_data": "Hello, World!",
  "source_device": "12345",
  "datetime": "2016-09-27T18:53:09.302915"
}
```

When sending JSON content, make sure the *Send JSON Content Type* checkbox
is checked. This sets the HTTP *Content-Type* header so that the receiving 
application knows to interpret the body as JSON.

It's also possible to include template variables in the destination URL.
This is useful for sending data as URL query string parameters.
For example, the URL:

```bash
https://example.com/webhook?device=<<device_id>>&data=<<data>>
```

Will send the `device_id` and `data` variables as query string parameters.


