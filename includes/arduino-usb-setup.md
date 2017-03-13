Connect the Dash to your computer with a USB cable. In the menu bar
go to *Tools* -> *Port* and select the appropriate serial or USB port. Typically
the ports are named as follows according to your operating system:

* **Windows** -- `COMx`
* **MacOS** -- `/dev/cu.usbmodemxxxx`
* **Linux** -- `/dev/ttyUSBx` or `/dev/ttyACMx`

where `x` is a system-specific identifier. If you are still unsure of which port
to use, disconnect the Dash and check which port disappears from the list. Then
re-connect the Dash and select that port.

{{#callout}}
If you're on Windows 8.1 or earlier and no port shows up when plugging in the Dash,
see our [Windows guide](/docs/guide/dash/windows/) for driver installation
instructions.
{{/callout}}
