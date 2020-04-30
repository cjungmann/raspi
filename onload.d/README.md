# The onload.d Directory

This directory is copied onto the newly-built SD card, and
the contents of this directory will used the first time the
RaspberryPi computer is started.  This directory is accessed
from the script *load_apps*, which can be found in this
project and will be copied to */etc/init.d* on the SD
card.

The assumption is that all of the regular files are script
files to be run, and that these scripts will perform additional
customized setup after the standard setup is completed.  The
scripts will be run in alphabetical order, so use names that
sort appropriately if the order of execution is significant.

If you need files that are not scripts, you can include
subdirectories in the *onload.d* directory.  The subdirectories
will be copied with the rest of the contents of *onload.d*.