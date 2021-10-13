# Using FreeBSD on Raspberry Pi

Starting with FreeBSD version 13.0 RELEASE, there is an sd image of
FreeBSD available for the Raspberry Pi 4.

There is a [FreeBSD Raspberry Pi page](https://wiki.freebsd.org/arm/Raspberry%20Pi)
that includes some helpful information for configuring that
device.

## Downloading and Installing

Runs scripts `bsd_download`, then run `bsd_build *dev-target*`.
As far as the *dev-target* argument for `bsd_build`, this is a
suitable device under the `/dev/` directory.  An appropriate value
is likely to be `sda` or `sdb` or some such.  Leave out the `/dev/`.

## Logging in For The First Time

The default user is **freebsd** with a password of **freebsd**.
This user does not have root privileges.  There is also a
pre-installed **root** user with the password **root**. (Make
sure you change both of these passwords before you let the
public at your computer.)

## Setup

The FreeBSD image will be lacking many utilities one normally
counts on being installed by default.  The following is a guide
to the most important.  You should log in as **root** to install
packages

- **Set root password** The root account on FreeBSD initially
  does not have a password.  You can and should login initially
  as root and set the password.  
  `root$ passwd`
  Having set the root password, you can use SSH to complete the
  following steps (**ssh** is installed and active by default).

- **Install the package manager**:  
  `pkg -y update && pkg -y upgrade`

- **Install favorite editor** for configuration.  
  I like Emacs, and I prefer to use it in the terminal.
  Install emacs with the following command:  
  `pkg install emacs-nox`

- **Add a sudo user (group *wheel*)**  
  - Install **bash** if you want the **bash** to be the default
  shell program.
  - Use `adduser` and answer the questions, remembering to add
    the user to the *wheel* group.
  - Create a password with `passwd <newuser-name>`.

- **Install and configure sudo**  
  - `pkg -y install sudo`
  - `visudo /usr/local/etc/sudoers`  
    - If you're not comfortable with  the **vi** editor to run
      **visudo**, you can use your favorite editor by setting
      environment variable **VISUAL**:  
     `export VISUAL=emacs; visudo`
   - To enable sudo for your sudo user, do at least one of the
     following:
     1. Add your user after `ROOT ALL=(ALL) ALL`, using the same
        formula replacing ROOT with your user name, or
     2. Assuming your user is in group *wheel*, you can uncomment
        the line with %wheel `ALL=(ALL) ALL`.
   - **Now you can SSH to the FreeBSD computer to continue
     configuring FreeBSD.**

- **Install GIT**
  This is the most important source-control program, one that
  allows you to download most important software on the internet.
  `pkg install git`

- Install GNU development software:  
  `sudo pkg install lang/gcc gmake fcgi-devkit-2.4.0_5 texinfo wget`