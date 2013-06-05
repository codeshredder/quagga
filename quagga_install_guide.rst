1) under ubuntu 13.04

::

   apt-get install g++
   apt-get install gawk
   apt-get install libreadline6-dev


::

   sed -i 's/#net.ipv4.ip_forward=1/net.ipv4.ip_forward=1/' /etc/sysctl.conf
   
   sysctl net.ipv4.ip_forward=1


2) download quagga
lastest verion is quagga-0.99.22.tar.gz
from http://www.nongnu.org/quagga/


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


会提示输入口令，我们通过/usr/local/etc/zebra.conf可以看到口令是zebra，enable口令也是zebra。输 入口令zebra，得到路由器用户模式提示符： 

::

   Router>


进入特权模式::

   Router> enable
   
   Password: 


提供了的路由命令::

   Router# ? 




6) RIP test





7) OSPF test



8) BGP test


