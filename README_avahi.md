# AVAHI

The **avahi** service allows computers on a network to
discover a specific computer by a hostname.

The Raspberry Pi OS includes this service by default,
but other distributions may need to install and configure
**avahi** to make a headless device discoverable using:

~~~sh
ping <hostname>.local
~~~

Where _<hostname>_ is replaced with the hostname of the
device.

My interest in this is to bring a headless Raspberry Pi to
the library and be able to access it using SSH.  For that
purpose, it may be helpful to change the hostname to a more
distinctive name to avoid confusion.

## INSTALLING AVAHI

~~~sh
sudo apt install avahi-daemon
~~~

You can set the string used for pinging Avahi in:
- **/etc/avahi/avahi-daemon.conf**
  Set the **host-name** parameter in the [SERVER] section.

- **/etc/avahi/hosts**
  Just put the desired name (or names) in the **hosts** file.
