==========================================================
  Quagga Usage Guide
==========================================================


Authors
==========

`codeshredder <https://github.com/codeshredder>`_ 

Reference
==========

Quagga Official Documentation:

http://www.nongnu.org/quagga/docs/docs-info.html


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

   cp /usr/local/etc/zebra.conf.sample /usr/local/etc/zebra.conf
   
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


3. RIP Test
==============

start rip deamon::

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


when finished,we can show the status to check if rip work well::

   # show ip route
   Codes: K - kernel route, C - connected, S - static, R - RIP,
       O - OSPF, I - IS-IS, B - BGP, A - Babel,
       > - selected route, * - FIB route
   
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
   
   # in linux shell
   # route

if working well, router2 will find some router1's information.


how to debug::

   # debug rip events
   # debug rip packet
   # configure  terminal
   (config)# log file /tmp/ripd.log
   
   #then exit to linux shell
   #tail -f /tmp/ripd.log




4. OSPF Test
==============

start ospf deamon::

   cp /usr/local/etc/ospfd.conf.sample /usr/local/etc/ospfd.conf
   
   ospfd -d
   
(notice: eth port must be up)

route1::

   #vtysh
   #configure  terminal
   (config)# interface  eth0
   (config-if)# ip address  192.168.0.100/24
   (config-if)# exit
   (config)# interface  eth1
   (config-if)# ip address  192.168.1.100/24
   (config-if)# exit
   (config)# router ospf
   (config-router)# router-id  192.168.0.100
   (config-router)# network 192.168.0.0/24 area 0
   (config-router)# network 192.168.1.0/24 area 1
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
   (config)# router ospf
   (config-router)# router-id  192.168.0.200
   (config-router)# network 192.168.0.0/24 area 0
   (config-router)# network 192.168.2.0/24 area 2
   (config-if)# exit
   (config)# exit
   #

when finished,we can show the status to check if ospf work well::

   # show ip route
   Codes: K - kernel route, C - connected, S - static, R - RIP,
          O - OSPF, I - IS-IS, B - BGP, A - Babel,
          > - selected route, * - FIB route
   
   O   192.168.0.0/24 [110/10] is directly connected, eth0, 00:00:18
   C>* 192.168.0.0/24 is directly connected, eth0
   O>* 192.168.1.0/24 [110/20] via 192.168.0.100, eth0, 00:00:13
   O   192.168.2.0/24 [110/10] is directly connected, eth1, 00:00:06
   C>* 192.168.2.0/24 is directly connected, eth1
   
   # show ip ospf  neighbor
   
         Neighbor ID Pri State           Dead Time Address         Interface            RXmtL RqstL DBsmL
   192.168.0.100       1 Full/DR         33.348s 192.168.0.100     eth0:192.168.0.200       0     0     0
   
   # in linux shell
   # route


5. BGP Test
==============

start bgp deamon::

   cp /usr/local/etc/bgpd.conf.sample /usr/local/etc/bgpd.conf
   
   bgpd -d
   
(notice: eth port must be up)

in default bgpd.conf, AS number has been set. Need to ignore by !::

   vi /usr/local/etc/bgpd.conf
   ! router bgp 7676


route1::

   #vtysh
   #configure  terminal
   (config)# interface  eth0
   (config-if)# ip address  192.168.0.100/24
   (config-if)# exit
   (config)# interface  eth1
   (config-if)# ip address  192.168.1.100/24
   (config-if)# exit
   (config)# router bgp 7675
   (config-router)# bgp  router-id  192.168.1.100
   (config-router)# network 192.168.1.0/24
   (config-router)# neighbor  192.168.0.200 remote-as  7676
   (config-router)# exit
   (config)# exit

route2::

   #vtysh
   #configure  terminal
   (config)# interface  eth0
   (config-if)# ip address  192.168.0.200/24
   (config-if)# exit
   (config)# interface  eth1
   (config-if)# ip address  192.168.2.200/24
   (config-if)# exit
   (config)# router bgp 7676
   (config-router)# bgp  router-id  192.168.2.200
   (config-router)# network 192.168.2.0/24
   (config-router)# neighbor  192.168.0.100 remote-as  7675
   (config-router)# exit
   (config)# exit


when finished,we can show the status to check if bgp work well::

   # show ip route
   Codes: K - kernel route, C - connected, S - static, R - RIP,
          O - OSPF, I - IS-IS, B - BGP, A - Babel,
          > - selected route, * - FIB route

   C>* 192.168.0.0/24 is directly connected, eth0
   B>* 192.168.1.0/24 [20/0] via 192.168.0.100, eth0, 00:00:21
   C>* 192.168.2.0/24 is directly connected, eth4
   
   # show ip bgp
   BGP table version is 0, local router ID is 192.168.3.5
   Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
                 r RIB-failure, S Stale, R Removed
   Origin codes: i - IGP, e - EGP, ? - incomplete
   
      Network          Next Hop            Metric LocPrf Weight Path
   *> 192.168.1.0      192.168.0.100            0             0 7675 i
   *> 192.168.2.0      0.0.0.0                  0         32768 i
   
   Total number of prefixes 2

   # in linux shell
   # route



6. Quagga Patches
==============





7. Licensing
============

This project is licensed under Creative Commons License.

To view a copy of this license, visit [ http://creativecommons.org/licenses/ ].

8. Contacts
===========

codeshredder  : evilforce@gmail.com
