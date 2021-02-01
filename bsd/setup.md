# Setting Up Raspberry Pi with FreeBSD

I'd like to be able to do a headless setup on a
Raspberry Pi.  This document shows how to copy an OS image,
find and log into, and install fundamental programs on a
Raspberry Pi 3.  I haven't been able to get a version 4 to
boot.

I found [this](https://minnie.tuhs.org/Blog/2019_06_27_FreeBSD_NAS.html)
very informative page.  I'm not preparing a NAS, but this
individual has documented many configurations in support
of NAS that may be useful to a general user.

## Prepare the Raspberry Pi with an new FreeBSD image:

- Download the compressed img.xz file
- Extract image with unxz: `unxz freebsd...img.xz`
- Use **dd** to copy image to an SD card
  - Unmount if mounted (check **mount** to see where it is)
  - Wipe the card `sudo wipefs -a /dev/sdx`  (replace *x* with appropriate letter).
  - `dd bs=4M if=freebsd...img of=/dev/sdx conv=fsync status=progress`
- Insert the prepared SD card to a Raspberry Pi.

## Attach to Computer with SSH

Although you can access the computer directly with a keyboard
and monitor, I think it's easier to connect with SSH, where you
can use your terminal program, where you have a lot more control
over how it's displayed.

The main problem is finding the IP address.  Using **nmap**
works sometimes.  On a workstation that shares the same LAN,
enter:

~~~sh
nmap -sn 192.168.0.1/24
~~~

and look for an unfamiliar IP address.

Alternatively, I log into my firewall and look at the
**dhcpd.leases** file for a new lease.  Knowing that the
hostname is *generic* will help find the appropriate lease.

## First Time in FreeBSD

Log in with **root**.  If you're using SSH, enter
`ssh root@192.168.0.xxx`
where IP address is replaced with the PI's actual IP address.

The password is *root*.  You'll want to change this later.

### Install *sudo*

Having **sudo** available makes your life easier.  Otherwise
you have to call `su -l` to become root when installing
software, with associated inconveniences.

- Load **sudo** for root access with regular signons.  
  `pkg install sudo`
- Enable *sudoers* by calling `visudo`.
  - `visudo`
  - Near the bottom of the file, find line  
    `# %wheel ALL=(ALL) ALL`
    - Use *j* to move down one line, *k* to move up one line.
  - *x* deletes a single character.  Press *x* twice to
    delete the *#* and the space.
  - Save the file by typing *:* to enter command mode,
    then entering *qw* to quit after saving changes.

### Create Administrative User

Create a new user of the **wheel** group.  Type `adduser`
and follow the instructions.  Make sure that your user
is in the **wheel** group, members of which will be able
to use **sudo** to perform root-privilege commands.

### Install Useful Programs

Call as many of the following commands to install the
associated programs.  You may find it more successful to
follow the listed order.

- `sudo pkg install git`
  We will use **git** to download programs that are not
  in the FreeBSD repositories.

- `sudo pkg install emacs-nox`
  The best text editor.  Using *nox* in the assumption
  that we're setting it up headless and will be using
  terminal.  Feel free to omit *-nox* for the full
  EMACS experience.

- 
