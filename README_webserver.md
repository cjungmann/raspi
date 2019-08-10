# Raspberry Pi Web Services Setup

As I write this README, I am planning to create a web
server out of a Raspberry Pi.  I want to experiment,
especially with:

- [LightTPD](https://www.lighttpd.net/)
  ~~~sh
  sudo apt-get install lighttpd lighttpd-doc
  ~~~

Support commands that are not already installed:

- The PCRe Library
   ~~~sh
   sudo apt-get install libpcre3 libpcre3-dev
   ~~~

I indend to use my framework, but I will put off installing
it until I've installed its dependencies.

- [Raspberry Pi MySQL Install](https://pimylifeup.com/raspberry-pi-mysql)
  ~~~sh
  sudo apt-get install mariadb-server
  ~~~


Now we can install the Schema Framework, starting with the
server object:

- [Schema Server](https://www.github.com/cjungmann/SchemaServer.git)
- [Schema Framework](https://www.github.com/cjungmann/schemafw.git)

