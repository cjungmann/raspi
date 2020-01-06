# Setup Raspberry Pi 3 for SSH on Linux

Being comfortable managing remote Linux computers using SSH and a console
window, I wanted to configure a headless Raspberry Pi for some
experiments.  While I did ultimately find online everything I needed to
know, I didn't see anything that listed all the necessary steps.  This
document aims to provide that.

In general, configuring a Raspberry Pi involves downloading and
installing an operating system on an SD card, making a few changes
to the SD card prior to the first boot-up, then using SSH to access
the device to make permanent changes to allow continued access.

Please note that these instructions are specifically for using a Linux
computer to configure the Raspberry Pi device.  The steps can be
accomplished on other platforms using tools found on those systems.
Adjust the instructions accordingly, using the online documentation
as necessary.

The two main sources of online information will be listed at the end of
this document.

## Setup Steps

- Download the operating system
  - Go to [OS download page](https://www.raspberrypi.org/downloads/).
  - Download the file that meets your needs.
  - Extract the **.img** file in an appropriate directory for further
    access.  I extracted the file to my Desktop.

- Create a new image on an SD card.  These steps are more completely
  spelled out [here](https://www.raspberrypi.org/documentation/installation/installing-images/linux.md).
  - Use a card reader or SD port on a Linux computer.
  - Invoke the **lsblk** command to see block devices on the computer.
    Identify the SD card from the output.
  - Use the **dd** command to copy the image to the SD card, replacing
    the *if* option with the system image file path *of* option with the
    device path.  It is important that the *of* option points to the SD
    device, not to one of its partitions.  That is, to */dev/sda*, and
    **not** */dev/sda/sda1*.  Use the following example as a hint.

    ~~~.sh
    $ sudo dd bs=4M if=2019-07-10-raspbian-buster-lite.img of=/dev/sda status=progress conv=fsync
    ~~~

    **Alternate method** use **build_sd** to install and configure
    the sd card.   See Below.

  - Use **sync** before removing the card to ensure it is done writing.
    - Since the directories are not mounted, you can't use **umount** to
      ensure all the writes are done.

  - Remove and reinsert the SD card to mount the partitions.

- Modify */boot* files before first boot for *network* and *ssh*.
  - Go to the boot directory (mounted at */media/chuck/boot* on my computer)
  - Create a file **wpa_supplicant.conf** in the */boot* directory.
    - Add the following contents:
      ~~~.sh
      country=us
      update_config=1
      ctrl_interface=/var/run/wpa_supplicant

      network={
         ssid="<name of your WiFi>"
         psk="<password for your WiFi>"
      }
      ~~~

    Don't be afraid to include two or more networks if your router
    supports multiple protocols, or if you have multiple wireless
    routers.  That will increase your chances of making the connection.

  - Create an empty file named **ssh**
    ~~~.sh
    $ touch ssh
    ~~~

  - Optional: Change the hostname.  The default hostname, *raspberrypi*
    may cause confusion if there are multiple Raspberry Pi devices on
    your network.  This is important for headless IP discovery.

    Change the hostname in */etc/hostname*, and if */etc/hostname* is
    changed, the local hostname in */etc/hosts* must also be changed
    to match.  Use the following commands by setting variable *hname*
    first, then copy and past the last three lines into a console.

    These changes can be made at the same time as the *wpa_supplicant.conf*
    and *ssh* files.  On my computer (that path for which should be
    changed according to the device from which it is being edited), the
    files are in */media/chuck/rootfs/etc*.

  - Unmount the */media/chuck/boot* and */media/chuck/rootfs* directories
    before removing the SD card.  Use either the **umount** command or
    a file manager program.

  - The **ssh** file is always deleted from the **/boot** directory.
    You will need to manually create the **ssh** file in the */boot*
    directory with another computer every time you need SSH access unless
    you configure your device to allow SSH.  See instructions below.

- Insert the SD Card into the Raspberry Pi and Power Up.
  - Only after a few failures did I discover I needed a more generous
    power supply.  The specs says that the device needs 5 volts DC and
    a minimum of 2.5 Amps.
  - Avoid turning off the device by removing the power cord.  Use SSH
    as described below, then issue the **halt** command:

    ~~~.sh
    $ sudo halt --poweroff
    ~~~

    or

    ~~~.sh
    $ sudo poweroff
    ~~~

  - On first boot-up
    - Before attempting to install any program, update the repositories: 
      `sudo apt-get update`
    - Install essential applications, like emacs: 
      `sudo apt-get install emacs-nox`

## Connect to Raspberry Pi with SSH

SSH is the best way to connect to a headless Raspberry Pi device.
The following instructions will help configure your Raspberry Pi
for SSH.

In some cases, it may be necessary to SSH over Bluetooth.  Look
at [this guide](README_bluetooth.md) for some ideas.

### Determine the IP address

This is not very convenient.  The MAC address is not printed on the
device, so you can't force your DHCP server to assign a specific IP
address.  You have to get the IP address from the device itself.

#### Easiest Headless Discovery Method

Your first attempt should be the easiest method.  Use the hostname
you set above (or *raspberrypi* if you didn't change it) with
**ping** to discover your device:

~~~sh
$ ping raspberrypi.local
~~~

or simply ssh to the pi with:

~~~sh
$ ssh pi@raspberrypi.local
~~~

If the command takes a long time to return, it's not working.
Press Control-C to exit and try the other methods below.

#### Better Headless Discovery Method

I saw this method on [this page](https://howchoo.com/g/njy4zdm3mwy/how-to-run-a-raspberry-pi-cluster-with-docker-swarm?utm_source=taboola&utm_medium=cpc&utm_campaign=int_pi_automated#find-the-ip-address-of-each-node)
about making a RaspberryPi cluster.  For that application, it was
necessary to find all of the ip addresses. The problem with the
**ping** method above that it only works for a single device.

Use the following command, adjusting the base IP address to match
your network.

~~~sh
sudo nmap -sn 192.168.0.1/24
~~~

The secret is the **-sn** option, that scans for devices using
**ping** and skips the port scan, greatly speeding up the process.
Look at the **man** page for **nmap**.

##### Tiny Login Script

This repository includes a tiny script, **sshpi** to uses the
ping method to discover the IP address of a Raspberry Pi on a
local network, then calls **ssh** using user **pi**, and offering
a reminder that the default password is *raspberry*.

While leaving the default user name and password enabled is a
safety concern, this script is a useful shortcut while configuring
the device.

#### Painful Headless Discovery Methods

Following are three methods for discovering the device's IP address.
There may be other ways to do it, but one of these three should be
sufficient to get the address.

- **ifconfig**: Attach a monitor and USB keyboard and login to the
  device.  Then use the command **ifconfig** to view the network
  device addresses.
- **dhcpd.leases** file:  If you have access to the DHCP server from
  which the IP addresses are assigned, you can peruse the
  **dhcpd.leases** file, usually found in the */var/lib/dhcp*
  directory.
- **nmap** command: Install and use the **nmap** command to scan the
  computers on your network.  Invoke the command with a network mask,
  then look at the output, eliminating familiar IP addresses to limit
  your consideration to new or unknown IP addresses.  The following
  call to **nmap** scans and lists the most common ports.

  ~~~.sh
  $ nmap -F 192.168.0.0/24
  ~~~

  This scan will take a while because it's also scanning each host's
  ports.  Look for a host with only an SSH port open, that is likely
  to be your Raspberry Pi device.

### Establish an SSH Session

You need a username and a password, along with the IP address, to
establish an SSH session.

The default username is **pi** and the default password is
**raspberry**.

Assuming that the IP address discovered in the previous step is
**192.168.0.74**, invoke **ssh** thus:

~~~.sh
$ ssh pi@192.168.0.74
~~~

Type in **raspberry** when asked for the password to complete
the login.

## Configure Raspberry Pi for Future Accesses

It is not very convenient to have to remove the SD card from the
Raspberry Pi device, mount it on another computer to create the
**/boot/ssh** file, unmount and return to the Raspberry Pi to 
connect to the device.  Instead, configure the device for SSH
access as shown below.  While you're at it, set a new password
for better security.

- Make SSH and other changes persistent.  SSH access through the **ssh**
  file only works once.  Permanently permit SSH.
  - Open the **raspi-config** tool.
  - Open option **5 Interfacing Options**.
  - Open interface option **P2 SSH**.
  - Turn on SSH access.

- Change password from default.  Don't leave the default password on
  your device!
  - Open the **raspi-config** tool.
  - Open option **1 Change User Password**.
  - Set the new password.

## Useful Raspberry Pi Utilities

The pre-built Raspberry Pi OS *Raspbian* includes some useful utilities.

- **vcgencmd** is a command that reports on the device.  Within the
  command, there are a couple of useful commands:
  - **vcgencd commands** returns a comma-separated list of vcgencmd
    commands.
  - **vcgencmd measure_temp** returns the current CPU temperature.
  - **vcgencmd get_throttled** returns 0 or 1 if the device is
    currently slowed for safety against excess heat.

- **raspi-config** shows a menu for setting various configuration
  options.

## BUILD_SD

This configuration script will install an image file to a target
SD card.  The desired image file must be in ths **raspi** directory
(where this README.md file resides), and you must know the path
in */dev* where the SD device is located (e.g. */dev/sda*).

The utility will do a simple-minded confirmation of the target,
find the latest **.img** file by filename, then use **dd** to
install the image.

One the image is installed, it will mount the **boot** partition
and copy files for initial setup, for WiFi and **ssh**.  Then it
mounts the **rootfs** partition and copies a couple of files in
the hope it can enable **ssh** over bluetooth.  This has failed
so far.

## Sources of Useful Information

I couldn't have figured this out without the following resources.
The online guides I found for headless configuration were not complete,
and I had to refer to the Raspberry Pi online documentation to 
finally 

The instructions on this page were derived through trial-and-error and,
when that failed, through reading online documentation.  Everything you
need to know will be found by reading documents starting at:
  - [The Raspberry Pi install directions](https://www.raspberrypi.org/documentation/installation/installing-images/linux.md)
  - [Raspberry Pi configuration pages](https://www.raspberrypi.org/documentation/configuration/)

