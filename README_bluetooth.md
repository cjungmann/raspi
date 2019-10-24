# Raspberry Pi Bluetooth

In order to configure a Raspberry Pi device in public library,
or any WiFi that needs a response from a terms-of-service page.
We want to connect to the device over SSH, but until the device
is attached to the WiFi, the normal discovery process is not
available.

Note that at the bottom of this page there is an **External Resources**
section with links to helpful pages.

## Get Device Bluetooth Information

Assuming that your device can be accessed using WiFi
in a friendly environment (like home), there are some
utilities that can help read bluetooth settings

- **hciconfig** is a bluetooth configuration tool
  This tool can also be used to query the device.


## External Resources

As I write this, I have no idea how to do this.  I will be
referring to documents I find on the internet, and I will
create a list of links to help remember how I do it (assuming
I can actually do it).

- [Mozilla Hacks](https://hacks.mozilla.org/2017/02/headless-raspberry-pi-configuration-over-bluetooth/)
  This is the first useful document that guided me.
  Changes to system resulting from following advice on
  this page:
  - Added two lines to */lib/systemd/system/bluetooth.service*
  - Created */lib/systemd/system/rfcomm.service* and added
    lines as suggested in the web guide.
