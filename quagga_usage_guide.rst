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

r1,r2 are connected with eth0.(quagga can not support vlan(like vlan1))

eth1 connected to another network.



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

start rip deamon:：

   cp /usr/local/etc/ripd.conf.sample /usr/local/etc/ripd.conf
   
   ripd -d
   

route1::

   #vtysh
   #configure  terminal
   (config)# interface  eth0
   (config-if)# ip address  192.168.0.100/24
   (config-if)# exit
   (config)# interface  eth1
   (config-if)# ip address  192.168.1.100/24
   (config-if)# exit
   (config)# router rip
   (config-router)# network 192.168.0.0/24
   (config-router)# network 192.168.1.0/24
   (config-if)# exit
   (config)# exit
   #


route2::

   #vtysh
   #configure  terminal
   (config)# interface  eth0
   (config-if)# ip address  192.168.0.200/24
   (config-if)# exit
   (config)# interface  eth1
   (config-if)# ip address  192.168.2.200/24
   (config-if)# exit
   (config)# router rip
   (config-router)# network 192.168.0.0/24
   (config-router)# network 192.168.2.0/24
   (config-if)# exit
   (config)# exit
   #

cmds are in different mode which can be distinguished by prompt, such as #, (config)#, (config-if)#, (config-router)# .

we must configure interface and router(rip,ospf,bgp.. are different.)

if want to save config, can use::

   # copy run start


when finished,we can show the status::

   # show ip route
   Codes: K - kernel route, C - connected, S - static, R - RIP,
       O - OSPF, I - IS-IS, B - BGP, A - Babel,
       > - selected route, * - FIB route
   
   K>* 0.0.0.0/0 via 10.141.123.1, eth4
   C>* 127.0.0.0/8 is directly connected, lo
   C>* 192.168.0.0/24 is directly connected, eth0
   R>* 192.168.1.0/24 [120/2] via 192.168.0.100, eth0, 01:13:34
   C>* 192.168.2.0/24 is directly connected, eth1
   
   # show ip rip
   Codes: R - RIP, C - connected, S - Static, O - OSPF, B - BGP
   Sub-codes:
         (n) - normal, (s) - static, (d) - default, (r) - redistribute,
         (i) - interface

        Network            Next Hop         Metric From            Tag Time
   C(i) 192.168.0.0/24     0.0.0.0               1 self              0
   R(n) 192.168.1.0/24     192.168.0.100         2 192.168.0.100     0 03:00
   C(i) 192.168.2.0/24     0.0.0.0               1 self              0

if working well, router2 will find some router1's information.


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
