---
id: 83
title: Adding and removing IP aliases
date: 2009-04-20T18:46:20+00:00
author: jacques
layout: post
guid: http://blog.linternux.com/?p=83
permalink: /index.php/2009/04/20/adding-and-removing-ip-aliases/
categories:
  - Network
tags:
  - ifconfig
---
You need to add an IP address on interface eth0 ? Use the following command to activate it.

<pre>ifconfig eth0:1 192.168.1.177 netmask 255.255.255.0</pre>

Now you want to delete it ? No problem, use the following command ;

<pre>ifconfig eth0:1 del 192.168.1.177</pre>

You can also create a file /etc/sysconfig/network-scripts/ifcfg-eth0:1 to have the IP automatically come up upon reboot.

<pre># cat /etc/sysconfig/network-scripts/ifcfg-eth0:1
TYPE=Ethernet
# Device Name
DEVICE=eth0:1
# Boot Protocol none,bootp,dhcp
BOOTPROTO=none
# IP Address
IPADDR=192.168.1.177
# Netmask
NETMASK=255.255.255.0
# Normal user cannot activate/deactivate it
USERCTL=no
# IPv6 Actvated
IPV6INIT=no
# Use the parent interface
ONPARENT=yes
# yes = Modify /etc/resolv.conf if DNS directive is set
#       If using DCHP, then yes is the default
# no  = Do not modify /etc/resolv.conf
PEERDNS=no</pre>