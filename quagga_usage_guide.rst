==========================================================
  Quagga Usage Guide
==========================================================


Authors
==========

`codeshredder <https://github.com/codeshredder>`_ 

Reference
==========



Table of Contents
=================

::

  0. What is it?
  1. Overview
  2. Install Quagga
  3. RIP Test
  4. OSPF Test
  5. BGP Test
  6. Quagga Patches
  7. Licensing
  8. Contacts

0. What is it?
==============

This is a quagga based router test model.


1. Overview
==============

nework topologies:

:Router: NICs
:r1: eth0 (192.168.0.100), eth1 (192.168.1.100)
:r2: eth0 (192.168.0.200), eth1 (192.168.2.200)



2. Install Quagga
==============


1) under ubuntu 13.04

::

   apt-get install g++
   apt-get install gawk
   apt-get install libreadline6-dev


::

   sed -i 's/#net.ipv4.ip_forward=1/net.ipv4.ip_forward=1/' /etc/sysctl.conf
   
   sysctl net.ipv4.ip_forward=1


2) download quagga

from http://www.nongnu.org/quagga/

lastest verion is quagga-0.99.22.tar.gz


3) install quagga

::

   tar xvf quagga-0.99.22.tar.gz
   
   cd quagga-0.99.22
   ./configure --enable-vtysh --enable-user=root --enable-group=root --enable-vty-group=root
   
   make
   make install



4) run

::

   cd /usr/local/etc/
   cp zebra.conf.sample zebra.conf

   zebra -d


5) configuration

use vtysh::

   vtysh

or use telnet::

   telnet localhost 2601

password can be found in "/usr/local/etc/zebra.conf".(default password is "zebra")

port can be found in "/etc/services".as below
::

   zebrasrv        2600/tcp                        # zebra service
   zebra           2601/tcp                        # zebra vty
   ripd            2602/tcp                        # ripd vty (zebra)
   ripngd          2603/tcp                        # ripngd vty (zebra)
   ospfd           2604/tcp                        # ospfd vty (zebra)
   bgpd            2605/tcp                        # bgpd vty (zebra)
   ospf6d          2606/tcp                        # ospf6d vty (zebra)
   ospfapi         2607/tcp                        # OSPF-API


if login successfully, prompt::

   Router>

use "enable" to admin mode.
::

   Router> enable
   
   Password: 


check all the cmd in this mode::

   Router# ? 
   clear        Reset functions
   configure    Configuration from vty interface
   copy         Copy from one file to another
   debug        Enable debug messages for specific or all part.
   disable      Turn off privileged mode command
   end          End current mode and change to enable mode
   exit         Exit current mode and down to previous mode
   list         Print command list
   no           Negate a command or set its defaults
   ping         Send echo messages
   quit         Exit current mode and down to previous mode
   show         Show running system information
   ssh          Open an ssh connection
   start-shell  Start UNIX shell
   telnet       Open a telnet connection
   terminal     Set terminal line parameters
   traceroute   Trace route to destination
   undebug      Disable debugging functions (see also 'debug')
   write        Write running configuration to memory, network, or terminal


3. RTP Test
==============




4. OSPF Test
==============



5. BGP Test
==============



6. Quagga Patches
==============





7. Licensing
============

This project is licensed under Creative Commons License.

To view a copy of this license, visit [ http://creativecommons.org/licenses/ ].

8. Contacts
===========

codeshredder  : evilforce@gmail.com
