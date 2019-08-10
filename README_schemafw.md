# Schema Framework On Raspberry Pi

This is a very specific guide that makes a comprehensive change
to the RPi.

In this directory is a file **install_stuff** that is a script
that downloads, installs, and configures software to bring the
RPi to a condition where it can server Schema Framework
applications.

Use **scp** to copy the *install_stuff* file to the RPi.
For the example, we are using variables to show default
values that can be changed before calling the main command.

~~~sh
hostname=pihost
piaddr=192.168.0.20
scp install_stuff @${hostname}:${piaddr}:install_stuff
~~~