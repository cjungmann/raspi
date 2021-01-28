# Prepare FreeBSD SD card

The following steps assume that they are taken in the
cloned directory in order to be able to find the
**bsd_setup** script for copying to the FAT drive.  Make
adjustments as appropriate if these steps are executed
elsewhere.

1. Acquire RaspberryPi FreeBSD image
   - Download the image
     - Get latest version from the [FreeBSD downloads page](https://www.freebsd.org/where/) (look under **SD Card Images** column).
   - Extract the image using **unxz**.
   - Copy and paste:  
     ~~~sh
     wget https://download.freebsd.org/ftp/releases/arm64/aarch64/ISO-IMAGES/12.2/FreeBSD-12.2-RELEASE-arm64-aarch64-RPI3.img.xz
     declare fname=$( ls FreeBSD*.xz )
     unxz "${fname}"
     ~~~

1. Copy OS image to SD
   - Identify SD card in `/dev` directory
   - Fully clean the SD card with `wipefs -a /dev/sdx`
   - Copy the image to the SD card with  
     `sudo dd bs=4M if=FreeBSD-12.2-RELEASE-arm64-aarch64-RPI3.img`  
     BE PATIENT with **dd**, the image is much larger than the
     Linux Raspberry Pi image.
   - Copy and paste.  The following depends on having set **devtarget**
     to the `/dev/sdx` device (replacing 'x' with the appropriate letter).
     For example,  
     `declare devtarget=/dev/sdb` 

     ~~~sh
     declare fname=$( ls FreeBSD*.img )
     sudo wipefs -a devtarget
     sudo dd bs=4M if=${fname} of=$devtarget status=progress conv=fsync
     ~~~

1. Copy **bsd_setup** script to FAT Drive
   - Copy and paste
     ~~~sh
     mkdir fatdir
     sudo mount /dev/sdb1 fatdir
     sudo cp bsd_setup* fatdir
     sudo umount fatdir
     rm -rf fatdir
     ~~~

1. Initial FreeBSD boot
   - Install SD card in Raspberry Pi and power up.
   - Login as **root**, password **root**.
   - Change to /boot/msdos
   - Run `bsd_setup.sh`
     - This will take a **long** time, especially when
       installing python.
   
