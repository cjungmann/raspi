# Install and Configure LightTPD on Raspberry Pi

A large part of my wanting a Raspberry Pi is to experiment
with different web-server configurations.  Using an Apache
alternative is near the top of my wish-list.

## LightTPD and FastCGI

I am preparing a web-server configuration script that prepares
a freshly-installed Raspberry Pi image to run as a web server
using the [Schema Framework](https://www.github.com/cjungmann/schemafw.git)
with the [Schema Server](https://www.github.com/cjungmann/SchemaServer.git).
As the Schema Server is a FastCGI server, it is necessary to
configure the Raspberry Pi for compiling and using a FastCGI
component.

A resource I will use to figure this out is at [LightTPD.net](https://redmine.lighttpd.net/projects/lighttpd/wiki/Docs_ModFastCGI).  I'm adding
the link here to help me return.


