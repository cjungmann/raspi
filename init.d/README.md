# The *init.d* Subdirectory

This directory can contain script files that customize the
SD card build.

The script, *build_sd*, attempts to call the scripts in this
directory after the home directory */home/pi* is prepared.

All normal files in this directory are assumed to be scripts
to be run.  If you need supporting files and scripts, you can
put them into a subdirectory under *init.d*.

The scripts will be run in alphabetical order.  If some scripts
must be run before others, select script file names that ensure
the order of execution.