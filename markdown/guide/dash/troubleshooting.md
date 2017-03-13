---
title: Troubleshooting
nav_sort: 4
layout: guide.hbs
lunr: true
tags:
---

If you are having problems with the Dash or connecting via cellular, this page lists
some commonly encountered issues.

### Dash Serial Connection Issues

- Ensure correct port settings/device in your terminal emulator program
- Ensure the Dash is running the latest firmware and the preloaded user
  program, [available here](/docs/downloads)
- **Linux users:** If receiving permissions errors, ensure your user is a
  member of the *dialout* group, and restart your system before retrying.
  Alternately, run your terminal emulator with root permissions by using 
  *sudo*.
- **Windows users:** If receiving permissions errors, try running your terminal
  emulator (PuTTY, GTKTerm, etc) with Administrator privileges
- **When using a USB-to-TTL level converter:** Try
  swapping the Tx and Rx lines between the converter and the Dash
- **When using a USB-to-TTL level converter:**
  Make sure that you are using the *Serial2* interface in your code and 
  not *Serial* or *SerialUSB*.


### Cellular Connectivity and Hologram Cloud Issues

- Make sure you have activated your SIM on the [Hologram
  Dashboard](https://dashboard.hologram.io) and that its corresponding device
  has a *LIVE* status.
- You may need to reload the [Logs
  page](https://dashboard.hologram.io/devices/logs) in the Dashboard 
  in order to see new incoming messages.
