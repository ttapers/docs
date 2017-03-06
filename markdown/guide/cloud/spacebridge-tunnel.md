---
title: Secure Tunneling With Spacebridge
nav_sort: 4
autotoc: true
layout: guide.hbs
lunr: true
---

Hologram provides a service called Spacebridge that allows you to create secure,
authenticated tunnels to send data to a device with a Hologram SIM card
connected to the cellular network. With Spacebridge, you can send inbound traffic 
to any port on the device.

### Spacebridge Instructions

#### Enabling Tunneling

Before establishing the Spacebridge tunnel, you must first enable
tunneling for your SIM.

From the Hologram Dashboard's [device list](https://dashboard.hologram.io/),
select the SIM that you wish to tunnel to.

{{{ image src="/wp-content/uploads/2016/11/device-tunnel-toggle.png" alt="Enable/disable tunneling toggle" }}}

Select the *Cloud and messaging* view and scroll down to the *Device
tunneling* section. If tunneling isn't already enabled, click the *Enable
tunneling* button.

{{#callout}}
Your network interface itself must also be configured to support inbound
connections. Web-based configurations for some USB modems (e.g. E303's HiLink mode)
usually do not support this. [Configure the
modem](/docs/guide/connect/usb-modem/) using PPP or similar to
expose full network functionality.
{{/callout}}

#### Establishing the Tunnel With the Spacebridge Client

Download the latest Spacebridge client from the [Downloads
page](/docs/downloads/#spacebridge-client).

Extract the downloaded package and run the extracted application. You will be
prompted for your API key, which can be found in [Account 
Settings](https://dashboard.hologram.io/account/apikey).

You can then configure the devices and ports you wish to tunnel to. Each row
corresponds to a single tunnel, so for most basic usage you'll only need to
configure a single row.

A tunnel forwards all connections from a port on your local computer to a port
on the remote device. For example, if you wish to log into the remote device via
SSH, you must forward a local port to the remote port 22 (the standard SSH port). 
To avoid ports already in 
use by your system's network services, always use local ports above 1024. In
this case we'll use port 5000:

{{{ image src="/wp-content/uploads/2016/08/spacebridgescreenshot.png"
    alt="Spacebridge tunnel configuration" }}}

Then select *Done* to initiate the tunnel. A new dialog box will confirm that
the tunnel has been established:

{{{ image src="/wp-content/uploads/2016/08/spacebridge-2.png" }}}

Open an SSH client and connect to port
5000 on localhost (127.0.0.1). It may take a few moments for the routing to get
established, so be patient if the initial connection fails.


#### Useful Command Line Arguments for Spacebridge Client

* `--text-mode` -- Display all prompts as text on the command line
instead of popping up a GUI so you can more easily run the client in a
server environment. 
* `--apikey` -- Specify your Hologram API
key on the command line. 
* `--forward` -- Specify a forward in the
format `<linkid>:<device port>:<forwarded local port>`. You can specify
this option multiple times. Use this in combination with `--text-mode`
to create a fully scripted tunneling setup.
* `--help` -- Display additional options

### Tunneling Without the Spacebridge Client

The officially supported Spacebridge client is a convenient wrapper around
Hologram API calls and an SSH client. Therefore, in environments where a GUI
application isn't appropriate, you can use these standard protocols to open a
tunnel directly.

These instructions are for Linux with OpenSSH and assume
that you've already enabled tunneling for your device in the dashboard (covered
in the section above).

#### Generate SSH Key and Upload to Hologram API

This section only needs to be run the first time you are setting up the
tunnel on this computer. If you already have an SSH keypair that you'd
like to use, skip to step 2.

1.  Use ssh-keygen to generate a SSH keypair:
    ```bash
    ssh-keygen -f spacebridge.key -b 4096
    ```
    You'll be asked if you want
    to put a password on the key. This is recommended for
    enhanced security.
2.  Upload your public key to the Hologram API. In the code below,
    replace *<APIKEY>* with your Hologram API key (Found on the
    Dashboard by clicking on the *Account* menu at the bottom-left). If you are
    using a key that you generated previously, replace the filename in
    the export command. Note that the filename ends in .pub, not .key.
    If you use the .key file this will upload your private key which you
    definitely don't want to do.

    ```bash
    export PUBKEY=`cat spacebridge.key.pub` curl -X POST -H "Content-Type: application/json" -d "{\"public_key\":\"$PUBKEY\"}" "https://dashboard.hologram.io/api/1/tunnelkeys?apikey=<APIKEY>"
    ```

    You should get a successful response back.

#### Open the Tunnel by Running SSH With Port Forwarding

1.  Look up the link ID for the SIM you are trying to tunnel to. You can
    find this on the Dashboard by clicking on the plus sign next to the
    SIM number.
2.  The command below will open up the tunnel. You can see in this
    command where you would replace the link ID with your own ID. In
    this case, port 22 is the port on the device, and port 5000 is the
    local port being forwarded. Replace spacebridge.key with whatever
    private key you are using. This should be key file that goes with
    the .pub file that you uploaded to the Hologram API.
    ```bash
    ssh -p 999 -L 5000:link10311:22 -N -i spacebridge.key htunnel@tunnel.hologram.io
    ```
3.  You should now be able to connect to port 22 on your device from
    port 5000 on the local host

