# BSD on Raspberry Pi

Having learned how much more stable BSD is than Linux, I
am strongly considering shifting at least some of my computing
life over to some flavor of BSD.  It seems particularly useful
in a server setting, where the difficulty in navigating the
user interface is less important.

An obvious starting point would be to create a new firewall
using BSD.  That requires hardware that supports at least
two Ethernet ports.  The Raspberry Pi, with a USB-to-Ethernet
dongle, should be an appropriate platform.  Especially the
Version 4, with Gigabit Ethernet and fast USB3 ports.

However, I have had trouble getting FreeBSD to sho wany
evidence that it is running on a RPi4.  I have FreeBSD
running on RPi3, but that has slow Ethernet and only USB2,
not enough performance for a firewall.

I'm considering an attempt to use OpenBSD rather than
FreeBSD on the strength of OpenBSD's project goal of running
on any hardware.  I found [this article](https://www.sudopigeon.com/raspberry-pi/os-install-installing-openbsd-on-a-raspberry-pi-4/)
that I may use as a guide to OpenBSD-on-RPi4.

Another possibility is to use NetBSD.  This [page](https://opensource.com/article/19/3/netbsd-raspberry-pi) and
[page](https://wiki.netbsd.org/ports/evbarm/raspberry_pi/)
may be useful guides to installing NetBSD on a RaspberryPi. 