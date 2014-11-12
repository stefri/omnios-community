---
layout: guide
title: Setup

partof: Networking
num: 1
outof: 2
---

### Setting up dynamic networking

First identify the physical interface on which you would like networking:

~~~ shell
# dladm show-phys
LINK         MEDIA                STATE      SPEED  DUPLEX    DEVICE
e1000g0      Ethernet             unknown    0      half      e1000g0
e1000g1      Ethernet             ?          1000   full      e1000g1
~~~

Physical interfaces that don't have a corresponding IP interface might not
show their state correctly (in this case e1000g0).

To create an IP interface, then create an address and set it to DHCP:

~~~ shell
# ipadm create-if e1000g0
# ipadm create-addr -T dhcp e1000g0/v4
# ipadm show-addr
ADDROBJ           TYPE     STATE        ADDR
lo0/v4            static   ok           127.0.0.1/8
e1000g0/v4        dhcp     ok           10.0.2.15/24
lo0/v6            static   ok           ::1/128
~~~


### Setting up static networking

Just as above, we need to create an interface (if it hasn't been created
already)

~~~ shell
# ipadm create-if e1000g0
# ipadm create-addr -T static -a 192.168.1.10/24 e1000g0/v4static
# ipadm show-addr
ADDROBJ           TYPE     STATE        ADDR
lo0/v4            static   ok           127.0.0.1/8
e1000g0/v4static  static   ok           192.168.1.10/24
lo0/v6            static   ok           ::1/128
~~~

Finally, set up DNS resolution. Put your nameserver into `/etc/resolv.conf`
and configure NSS to read DNS from that file.

~~~ shell
# echo 'nameserver 192.168.1.1' >> /etc/resolv.conf
# cp /etc/nsswitch.conf{,.bak}
# cp /etc/nsswitch.{dns,conf}
~~~

Ready.
