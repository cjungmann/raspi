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
    

## Connect to Raspberry Pi with SSH

SSH is the best way to connect to a headless Raspberry Pi device.
The following instructions will help configure your Raspberry Pi
for SSH.

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

If the command takes a long time to return, it's not working.
Press Control-C to exit and try the other methods below.

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

