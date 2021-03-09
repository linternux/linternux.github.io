---
id: 577
title: Setting up your own NTP server
date: 2009-07-24T13:38:43+00:00
author: jacques
layout: post
guid: http://linternux.com/?p=577
permalink: /index.php/2009/07/24/setting-up-ntp-server/
categories:
  - Network
tags:
  - Network
  - NTP
---
As a system administrator, we must ensure that time synchronization is working at all time. Time synchronization have been proven to be helpful when we try to trace a chain of events that occurs on multiple servers or when trying to synchronize time sensitive-transactions. In this article, we will setup one NTP (Network Time Protocol) server running on RedHat/Centos 5, but it could be applied to the major Linux distribution.  As always, I have tried to make the article to be used a reference source, so that we can come back when ever you will play with the NTP tools.

The configuration have been done on my Linux server at home (gumby &#8211; 192.168.1.101).  We will setup a NTP   that will synchronize with NTP servers on the internet and it will answer NTP query on my internal network (192.168.1.0/24). This will allow desktop or laptop to synchronize with this NTP server.

### Brief history of NTP

The NTP protocol was born on the Unix operating system around the 1980&#8217;s and have been initiated by [Dave L. Mills](http://www.eecis.udel.edu/~mills/ "Dave l> Mills Home Page"). Currently there are two versions of NTP that can be used intermixed, the version 3 is very stable and it run on a number of operating systems. The version 4 have some enhancements and better support for the latest operating systems. There is also a simplified version of NTP called SNTP (Simple Network Time Protocol [RFC2030](http://www.faqs.org/rfcs/rfc2030.html "RFC2030 - SNTP")), but it provides a reduced precision due to a simpler algorithms. Some earlier version of NTP included some programs starting with xntp (like xntpd), with version 4 the naming convention were changed and now all the ntp programs included with package, start with ntp. For more information you can consult the [NTP home page](http://www.ntp.org/ "NTP Home page").

### Functionality overview

NTP use a hierarchical time synchronization structure. The hierarchical level of the time synchronization are called **stratum**. At the top of this hierarchy, there is a daemon that provide the most accuracy. [Atomic clock](http://en.wikipedia.org/wiki/Atomic_clock "Atomic Clock Wiki") are stratum zero, it is by far the most accurate method of keeping time with a precision of less than a second lost every 100 million years.  All NTP time server devices are referred to as stratum 1 devices. This is because they are on the next level (strata) down from an atomic clock (a stratum 0 device). Devices that a [NTP time server](http://www.galsys.co.uk/) synchronises are known as stratum 2 devices and these too can distribute their time signal to other devices which become stratum 3 and so on.

In large organization, they should be at least one or two NTP servers that synchronize with time server on the internet NTP servers (probably located in the DMZ).  Some NTP servers located in the corporate network, could then synchronize their time with the NTP servers located in the DMZ and be used as corporate NTP servers. This will ensure that all the clients have the same date and time.  Before continuing, I would like to state that the NTP protocol use **UDP port 123.** To be able to synchronize with its trusted time servers and to respond to client query the firewall configuration must allow UDP traffic to destination port 123.

<!--more-->

### Setting your time zone

The first we must check, is the time zone define on our system, it have to be define correctly. My server is located in Montreal and is define accordingly.

<pre>root@gumby:/# <strong>cat /etc/sysconfig/clock</strong>
# The ZONE parameter is only evaluated by system-config-date.
# The timezone of the system is defined by the contents of /etc/localtime.
ZONE="<strong>America/Montreal</strong>"
UTC=<strong>true</strong>
ARC=false
root@gumby:/#</pre>

If your is not setup correctly, you can use the change your time zone by using the command &#8220;timeconfig&#8221; that will display an ASCII text menu or use the GUI version by typing the command &#8220;system-config-time&#8221;. These programs modify the content of the files /etc/sysconfig/clock (ascii format) and /etc/localtime (binary format).

### NTP Software

Verify that the NTP package is installed on your server by using the &#8220;**rpm -qi ntp**&#8221; command. If not,  install it using the command &#8220;**yum install ntp**&#8221; or &#8220;**up2date &#8211;nox -i ntp**&#8221;  if you are using RedHat/Centos V3 or V4.

<pre>root@gumby:/etc# <strong>rpm -qi ntp</strong>
Name        : ntp                          Relocations: (not relocatable)
Version     : 4.2.2p1                           Vendor: Red Hat, Inc.
Release     : 9.el5_3.2                     Build Date: Thu 14 May 2009 09:01:12 AM EDT
Install Date: Thu 02 Jul 2009 09:29:46 PM EDT      Build Host: hs20-bc1-5.build.redhat.com
Group       : System Environment/Daemons    Source RPM: ntp-4.2.2p1-9.el5_3.2.src.rpm
Size        : 2540868                          License: distributable
Signature   : DSA/SHA1, Fri 15 May 2009 09:53:46 AM EDT, Key ID 5326810137017186
Packager    : Red Hat, Inc. &lt;http://bugzilla.redhat.com/bugzilla&gt;
URL         : http://www.ntp.org
Summary     : Synchronizes system time using the Network Time Protocol (NTP).
Description :
The Network Time Protocol (NTP) is used to synchronize a computer's
time with another reference time source. The ntp package contains
utilities and daemons that will synchronize your computer's time to
Coordinated Universal Time (UTC) via the NTP protocol and NTP servers.
The ntp package includes ntpdate (a program for retrieving the date
and time from remote machines via a network) and ntpd (a daemon which
continuously adjusts system time).

Install the ntp package if you need tools for keeping your system's
time synchronized via the NTP protocol.
root@gumby:#</pre>

### Choosing our internet NTP servers

It is a good practice that our NTP server synchronize with more that one servers. There is a project called [NTP Project Pool](http://www.pool.ntp.org/ "The NTP Pool Cluster Project") that is a big virtual cluster of NTP server spread across the world. In [North-America](http://www.pool.ntp.org/zone/north-america "NTP Servers in North America") they have more that 575 NTP servers and more than 50 in [Canada](http://www.pool.ntp.org/zone/ca "NTP Cluster in Canada"). We will use the Canadian cluster to setup our NTP server. You will find the ntp servers name at the top of  the [Canadian virtual cluster page](http://www.pool.ntp.org/zone/ca "NTP Cluster for Canada").  The IP address assigned to these names always change, this spread the load within the cluster and allow us to have a good response from these NTP servers. Below is the result of two &#8220;nslookup&#8221; of &#8220;0.ca.pool.ntp.org&#8221;, you will notice that the IP returned are not the same for the two &#8220;nslookup&#8221;. These servers are normally have a stratum of 2 or 3. If you wish to use a even more accurate NTP server, I suggest visiting this page that contain a [list of stratum one server](http://support.ntp.org/bin/view/Servers/StratumOneTimeServers "Stratum One server") on the internet and choose one or two servers located near you.

<pre>root@gumby:~# <strong>nslookup 0.ca.pool.ntp.org</strong>
Server:         192.168.1.101
Address:        192.168.1.101#53

Non-authoritative answer:
<span style="color: #ff0000;"><strong>Name:   0.ca.pool.ntp.org
Address: 66.96.30.35</strong></span>
Name:   0.ca.pool.ntp.org
Address: 174.142.75.135
Name:   0.ca.pool.ntp.org
Address: 206.248.189.157
Name:   0.ca.pool.ntp.org
Address: 209.139.209.82
Name:   0.ca.pool.ntp.org
Address: 216.221.88.81

root@gumby:~# <strong>nslookup 0.ca.pool.ntp.org</strong>
Server:         192.168.1.101
Address:        192.168.1.101#53

Non-authoritative answer:
<strong><span style="color: #ff0000;">Name:   0.ca.pool.ntp.org
Address: 216.221.88.81</span></strong>
Name:   0.ca.pool.ntp.org
Address: 66.96.30.35
Name:   0.ca.pool.ntp.org
Address: 174.142.75.135
Name:   0.ca.pool.ntp.org
Address: 206.248.189.157
Name:   0.ca.pool.ntp.org
Address: 209.139.209.82
root@gumby:~#</pre>

### NTP server configuration file

The NTP configuration file is called ntp.conf and is located in the /etc directory. Here is the configuration file for my home network. We will be using the 4 virtual servers of the Canadian NTP pool.

<pre>root@gumby:~# cat /etc/ntp.conf
# Default restriction
# kod           When access violation occurs send the "Kiss Of Death" - Drop Packet
# nomodify      Deny queries which attempt to modify the state of the server.
# nopeer        Deny packets which would result in mobilizing a new association.
# noquery       Deny Queries.
# notrap        Deny trap service -  subsystem of ntpdq control message protocol
#               intended for use by remote event logging programs.
<strong>restrict default kod nomodify notrap nopeer noquery</strong>

# Use public servers from the pool.ntp.org project.
# Please consider joining the pool (http://www.pool.ntp.org/join.html).
<strong>server 0.ca.pool.ntp.org
server 1.ca.pool.ntp.org
server 2.ca.pool.ntp.org
server 3.ca.pool.ntp.org</strong>

# Permit all access over the loopback interface.
<strong>restrict 127.0.0.1</strong>

# Hosts on local network are less restricted (Can Query)
<strong>restrict 192.168.1.0 mask 255.255.255.0 nomodify notrap</strong>

# Undisciplined Local Clock. This is a fake driver intended for backup
# and when no outside source of synchronized time is available.
<strong>fudge   127.127.1.0 stratum 10</strong>

# Drift file.  Put this in a directory which the daemon can write to.
<strong>driftfile /var/lib/ntp/drift</strong></pre>

  * For security reason, we disallow access to our NTP server. This line &#8220;**restrict default kod nomodify notrap nopeer noquery**&#8221; prevent clients, servers or hacker to query our NTP configuration.  (See explanation of each flag see the comment included in example above).
  * We then specify the server names we took from the [NTP project pool, for cluster of servers located in Canada](http://www.pool.ntp.org/zone/ca "NTP Cluster for Canada") &#8220;**server 0.ca.pool.ntp.org &#8230; server 3.ca.pool.ntp.org**&#8220;. We are using four servers names that are actually a pool of more than 50 servers located in Canada, this will ensure that we will always a quick response to our queries.
  * We give full access to the local host &#8220;**restrict 127.0.0.1**&#8220;.
  * With the line &#8220;**restrict 192.168.1.0 mask 255.255.255.0 nomodify notrap**&#8220;, we permit query but no modification or trap request to our internal network. In the example, it is my home network (192.168.1.0/24). You need to replace the IP and netmask by the network that you wish to allow synchronization with this server.
  * The line &#8220;**fudge 127.127.1.0 stratum 10**&#8220;, in a &#8220;just in case&#8221; line. If connection with the outside world is not working (internet network problem), the NTP service will continue to work and your internal server will still be able to synchronize with your server.
  * The line &#8220;**driftfile /var/lib/ntp/drift**&#8221; is added because the NTP daemon computes the drift of the system clock compared to the reference time. The daemon could then can save the drift rate to a file to have it available at the next restart. If the daemon is maintaining the drift file, this will increase synchronization speed.

### Starting NTP service

We want to make sure that the NTP service start at each system reboot. Type the following command to activate the service for system run level 3 and 5.

<pre>root@gumby:~# <strong>chkconfig --level 35 ntpd on</strong></pre>

The NTP Service can be stopped, started or restarted using the following command ;

<pre>root@gumby:~# <strong>service ntpd stop</strong>
Shutting down ntpd:                                        [  OK  ]
root@gumby:~# <strong>service ntpd start</strong>
Starting ntpd:                                             [  OK  ]
root@gumby:~# <strong>service ntpd restart</strong>
Shutting down ntpd:                                        [  OK  ]
Starting ntpd:                                             [  OK  ]
root@gumby:~#</pre>

Every time we make change to the configuration file we need to do a restart of the NTP service for the changes to take effect on the running process.

### Initial Synchronization

If your system date and time is very different from the actual time the NTP daemon may terminate. We should run the &#8220;ntpdate -u&#8221; command to force the system clock to become synchronize with our NTP cluster. If you encounter any problem with your time server, remember to check the &#8220;/var/log/messages&#8221; file for error messages.

To test our NTP server configuration we will change the date and time of our server to January the 1th of 2008 at 20:05:10 and use the &#8220;ntpdate -u&#8221; command to bring it back to the actual date and time.

<pre>root@gumby:~# <strong>date</strong>
Fri Jul 24 10:09:53 EDT <strong><span style="color: #ff0000;">2009</span></strong>

root@gumby:~# <strong>date 010120052008.10</strong>
Tue Jan  1 20:05:10 EST <span style="color: #ff0000;"><strong>2008</strong></span>

root@gumby:~# <strong>date</strong>
<strong><span style="color: #ff0000;">Tue Jan  1 20:05:18 EST 2008</span></strong>

root@gumby:~# <strong>ntpdate -u  0.ca.pool.ntp.org</strong>
24 Jul 10:10:19 ntpdate[1820]: step time server 132.214.200.120 offset 49208692.700394 sec
root@gumby:~# <strong>ntpdate -u  0.ca.pool.ntp.org</strong>
24 Jul 10:10:23 ntpdate[1821]: adjust time server 24.215.0.24 offset -0.031689 sec

root@gumby:~# <strong>date</strong>
<strong><span style="color: #ff0000;">Fri Jul 24 10:10:25 EDT 2009</span></strong>
root@gumby:~#</pre>

### Checking our NTP server status

The best way to confirm that NTP is working is to use the [ntp utility](https://www.eecis.udel.edu/~mills/ntp/html/ntpq.html "ntpq man page"). This command can be use to check the NTP service status on either the local server or on a remote server. We will use the &#8220;ntpq -p&#8221;  to print the status of our local NTP server. Looking at this result we can that the preferred time server was &#8220;woodlane.webcon&#8221;, because it have a &#8220;*&#8221; at the beginning of the line and that it is a stratum 3 server.

<pre>root@gumby:~# <strong>ntpq -p</strong>
 <strong>remote           refid          st t when poll reach   delay   offset  jitter</strong>
==============================================================================
-ntp1.sscgateway 209.87.233.53    3 u  367 1024  377   21.003  -111.05  17.218
<strong><span style="color: #0000ff;">*</span></strong>woodlane.webcon 132.246.168.164  3 u  645 1024  377   14.413   10.323   1.840
+chavez.x2a.org  132.246.168.164  3 u  393 1024  377   33.589   12.340   0.097
+ns1.ptpbroadban 192.135.48.21    3 u  146 1024  337   30.564   26.666   1.955
root@gumby:~#</pre>

The output above is showing that we have 4 references time sources, this what we expected.

  * The **remote column** indicate the name of the NTP server. We can also use the command &#8220;ntpq -pn&#8221; to display the IP instead of the name.  
    If the first character at the beginning of each is not blank, it is used as a qualifier for the time source on the line.</p> 
      * &#8220;*****&#8221; indicate that this server is the **preferred time source** for our NTP daemon.
      * &#8220;**+**&#8221; indicate that this server is a consider as a **high quality time source** and could be used if the preferred time source is not available.
      * &#8220;-&#8221; indicate that this server is **discarded** by the clustering algorithm as an outlyer
  * The &#8220;**refid**&#8221; does not indicate an IP address but a reference to the type of clock the server is using.  
    The &#8220;**st**&#8221; column indicate the stratum number of the server. All servers have a stratum of 3.
  * The **when** and the **poll** column are related. Each time NTP daemon query the time server identified on the same line it increment the **when** counter until it reach the **poll** number, at that point the when counter get rest to zero. The result of each cycle are used as a measure to the clock&#8217;s quality and availability.
  * The **reach** column shows the status of the availability register.This ****column must be interpreted as an 8 bit shift register, displayed in octal. When the NTP service is started the value of the reach column is set to zero. Each time a query is successful a &#8216;1&#8217; is shitfted to the right, so after the first query it contains a value of 1, after the second a value of 3, after the third a value of 7 (0,1,3,7,17,37,77,177,377). When it reach a **value of 377 this means that the last eight queries were successful**.
  * The remaining entries show the latest **delay**, **offset** and **jitter** in milliseconds, they show timing values which are derived from the query results. The **delay** is calculated from the roundtrip time of the NTP query. The **offset** is the difference between our server and the reference time server. The **jitter** value should be under 100 and indicates the magnitude of jitter between several time queries.

We have now a working NTP server running on our Linux server.

Hope that this article have been of interest for you and hope to see you soon.