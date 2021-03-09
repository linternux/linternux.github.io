---
id: 526
title: Viewing and changing netword card setting
date: 2009-07-05T11:52:57+00:00
author: jacques
layout: post
guid: http://linternux.com/?p=526
permalink: /index.php/2009/07/05/view-change-nic-setting/
categories:
  - Network
tags:
  - ethtool
  - Network
---
### **<span style="color: #0000ff;">Checking our network card current setting</span>**

There are two commands that we can use to check our current network card setting. The first command is &#8220;mii-tool&#8221;, it  has been around almost since the beginning of Linux and will be depreciated in the future version of Linux. If we don&#8217;t specify any parameter to the &#8220;mii-tool&#8221; command, we will get one line of information per network card. It will display the current speed of the network card, the duplex mode it&#8217;s using and if you have a link or not.  If we don&#8217;t have a link up (no link) and it is suppose to be, I suggest to verify that your cable is connected to the server and that the port on the network switch is activate.  If this is the case, you may want to try another network cable and see if the link come up.

<pre># <strong><span style="color: #0000ff;">mii-tool</span></strong>
eth0: <strong><span style="color: #0000ff;">100 Mbit, full duplex, link ok</span></strong>
eth1: 100 Mbit, full duplex, no link
#</pre>

If we add the verbose parameter (-v), we will get some additional information about each network card.

<pre># <strong><span style="color: #0000ff;">mii-tool -v</span></strong>
<span style="color: #0000ff;"><strong>eth0</strong></span>: 100 Mbit, full duplex, link ok
 product info: vendor 00:aa:00, model 50 rev 0
<strong><span style="color: #0000ff;"> basic mode:   100 Mbit, full duplex
 basic status: link ok</span>
</strong> capabilities: 100baseTx-FD 100baseTx-HD 10baseT-FD 10baseT-HD
 advertising:  100baseTx-FD 100baseTx-HD 10baseT-FD 10baseT-HD flow-control
<strong><span style="color: #0000ff;">eth1:</span></strong> 100 Mbit, full duplex, no link
 product info: vendor 00:10:18, model 23 rev 6
<span style="color: #0000ff;"><strong> basic mode:   100 Mbit, full duplex
 basic status: no link</strong></span>
 capabilities: 100baseTx-FD 100baseTx-HD 10baseT-FD 10baseT-HD
 advertising:  100baseTx-FD 100baseTx-HD 10baseT-FD 10baseT-HD flow-control
#</pre>

I would suggest using the “ethtool” command instead of the depreciated “mii-tool”. The “mii-tool” may not work with some of the newer network card and  may be excluded from future version of RedHat/Centos/Fedora. With the “ethtool” command,  we need to specify the name of the interface that we want to see the current state. In all of our example, we will use the interface &#8220;eth0&#8221;.

<pre># <strong><span style="color: #000000;">e<span style="color: #0000ff;">thtool eth0</span></span></strong>
Settings for eth0:
 Supported ports: [ TP MII ]
 Supported link modes:   10baseT/Half 10baseT/Full
 100baseT/Half 100baseT/Full
 Supports auto-negotiation: Yes
 Advertised link modes:  10baseT/Half 10baseT/Full
 100baseT/Half 100baseT/Full
<strong><span style="color: #0000ff;"> Advertised auto-negotiation: No
 Speed: 100Mb/s
 Duplex: Full</span></strong>
 Port: MII
 PHYAD: 1
 Transceiver: internal
 <span style="color: #993300;"><strong>Auto-negotiation: off</strong></span>
 Supports Wake-on: g
 Wake-on: g
 Current message level: 0x00000007 (7)
<strong><span style="color: #0000ff;"> Link detected: yes</span></strong>
#</pre>

### <span style="color: #0000ff;"><!--more-->Display driver information</span>

If we want to see some driver information about our network card, use the “-i” parameter next to the &#8220;ethtool&#8221; command.

In this example, we can see that the driver we are using for this card is “e100&#8243;.  Effectively, if we use the “lsmod” (list module) we found out the the “e100&#8243; is actually loaded.

<pre># <span style="color: #0000ff;"><strong>ethtool -i eth0</strong>
</span><strong><span style="color: #0000ff;">driver: e100
version: 3.5.10-k2-NAPI</span></strong>
firmware-version: N/A
bus-info: 0000:05:08.0
#
#<strong> <span style="color: #0000ff;">lsmod | grep e100</span></strong>
<span style="color: #0000ff;"><strong>e100                   </strong></span>36809  0
mii                     9409  2 3c59x,<strong><span style="color: #0000ff;">e100</span></strong>
#</pre>

### <span style="color: #0000ff;">Display statistics information</span>

<span style="color: #0000ff;"><span style="color: #000000;">With the &#8220;-S&#8221; parameter,  we can display statistics about our network card. </span></span>

<span style="color: #0000ff;"><span style="color: #000000;">This information could be valuable if you are experimenting some problem with that interface. It will confirm your doubt or it will indicate that you should check somewhere else to identify the problem.</span><br /> </span>

<pre># <span style="color: #0000ff;"><strong>ethtool -S eth0</strong></span>
NIC statistics:
 rx_packets: 12684
 tx_packets: 7638
 rx_bytes: 14990414
 tx_bytes: 621100
 rx_errors: 8
 tx_errors: 0
 rx_dropped: 0
 tx_dropped: 0
 multicast: 0
 collisions: 0
 rx_length_errors: 7
 rx_over_errors: 0
 rx_crc_errors: 1
 rx_frame_errors: 0
 rx_fifo_errors: 0
 rx_missed_errors: 0
 tx_aborted_errors: 0
 tx_carrier_errors: 0
 tx_fifo_errors: 0
 tx_heartbeat_errors: 0
 tx_window_errors: 0
 tx_deferred: 0
 tx_single_collisions: 0
 tx_multi_collisions: 0
 tx_flow_control_pause: 0
 rx_flow_control_pause: 0
 rx_flow_control_unsupported: 0
 tx_tco_packets: 0
 rx_tco_packets: 0
#</pre>

### <span style="color: #0000ff;">Network adapter self test</span>

<span style="color: #000000;">You can also run a self test on the network card.  Beside the interface name you can specify two types of tests:  <span style="color: #0000ff;"><strong>offline</strong> </span>(default) means to perform full set of tests possibly causing normal operation interruption during the tests, <span style="color: #0000ff;"><strong>online</strong> </span>means to perform limited set of tests do not interrupting normal adapter operation.</span> <span style="color: #0000ff;"></span>

<pre># <strong><span style="color: #0000ff;">ethtool -t eth0 online</span></strong>
The test result is PASS
The test extra info:
Link test     (on/offline)       0
Eeprom test   (on/offline)       0
Self test        (offline)       0
Mac loopback     (offline)       0
Phy loopback     (offline)       0

# <span style="color: #0000ff;"><strong>ethtool -t eth0 offline</strong></span>
The test result is PASS
The test extra info:
Link test     (on/offline)       0
Eeprom test   (on/offline)       0
Self test        (offline)       0
Mac loopback     (offline)       0
Phy loopback     (offline)       0
#</pre>

### <span style="color: #0000ff;">Changing network card setting interactively</span>

Below we change the network card setting from the command line. I strongly recommend doing these changes on the console,  if not you may loose your network connection with the your server.  
In this example we will set the interface &#8220;eth0&#8221; speed to 10Mb/s and the duplex mode to half. After doing the change we use again the &#8220;ethtool&#8221; command to verify that our change have took place.

<pre># <strong><span style="color: #0000ff;">ethtool -s eth0 speed 10 duplex half</span></strong>
# <strong><span style="color: #0000ff;">ethtool eth0</span></strong>
Settings for eth0:
Supported ports: [ TP MII ]
Supported link modes:   10baseT/Half 10baseT/Full
100baseT/Half 100baseT/Full
Supports auto-negotiation: Yes
Advertised link modes:  10baseT/Half 10baseT/Full
100baseT/Half 100baseT/Full
<span style="color: #000000;">Advertised auto-negotiation: No</span><strong><span style="color: #0000ff;">
Speed: 10Mb/s
Duplex: Half</span></strong>
Port: MII
PHYAD: 1
Transceiver: internal
<span style="color: #0000ff;"><strong>Auto-negotiation: off</strong></span>
Supports Wake-on: g
Wake-on: g
Current message level: 0x00000007 (7)
Link detected: yes
#</pre>

We will now set back our network card speed back to 100Mb/s, the duplex mode to &#8220;Full&#8221; and activate the speed autonegociate.

Again we verify that our change have took place by issuing the &#8220;ethtool -s eth0&#8221; command.

<pre># <span style="color: #0000ff;"><strong>ethtool -s eth0 speed 100 duplex full autoneg on</strong>
</span># <strong><span style="color: #0000ff;">ethtool eth0</span></strong>
Settings for eth0:
 Supported ports: [ TP MII ]
 Supported link modes:   10baseT/Half 10baseT/Full
 100baseT/Half 100baseT/Full
 Supports auto-negotiation: Yes
 Advertised link modes:  100baseT/Full
 Advertised auto-negotiation: Yes
<span style="color: #0000ff;"><strong> Speed: 100Mb/s
 Duplex: Full</strong></span>
 Port: MII
 PHYAD: 1
 Transceiver: internal
<span style="color: #0000ff;"><strong> Auto-negotiation: on</strong></span>
 Supports Wake-on: g
 Wake-on: g
 Current message level: 0x00000007 (7)
 Link detected: yes
#</pre>

### **<span style="color: #0000ff;">Setting speed and duplex at boot time</span>**

If we want to set the network card setting permanently, so they are preserve even after rebooting the the server, we need to open the file:

<pre># vi /etc/sysconfig/network-scripts/ifcfg-eth0</pre>

Append following line:

<pre>ETHTOOL_OPTS="speed 100 duplex full autoneg off"</pre>

If you want that change to become effective now, you will need to restart the network service. Be aware that people and/or processes running on the system may be affected by this restart.

<pre># service network restart</pre>

Hoping this article will be prove to be useful and see you soon.