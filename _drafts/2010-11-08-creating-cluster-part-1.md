---
id: 1242
title: 'Creating a Red Hat Cluster: Part 1'
date: 2010-11-08T21:22:51+00:00
author: jacques
excerpt: ' '
layout: post
guid: http://linternux.com/?p=1242
permalink: /index.php/2010/11/08/creating-cluster-part-1/
aktt_notify_twitter:
  - 'yes'
aktt_tweeted:
  - "1"
categories:
  - Cluster
tags:
  - cluster
---
This is the first of a series of articles that will demonstrate how to create a Linux cluster using Red Hat/CentOS  5.5 distribution. When we  created our first cluster at the office, we were searching for some Red Hat cluster setup information on the internet. To my surprise, we could not find a lot of  article or user experience on that topic. So I hope, this series of articles will benefit to the community of users trying or wanting to create their own cluster.

### The cluster hardware

Our cluster will have 3 HP servers, they will all have 4GB of memory, 36GB of mirrored internal disks, one Qlogic fiber card connected to our SAN system, 2 network cards and for our fencing device we will use the on-board [HP ILO](http://h18000.www1.hp.com/products/servers/management/ilo/) (Integrated Light Out). This is my setup, yours does not need to be the same.  You do not need to use HP server to build a cluster, you do not need to have mirrored disks (although recommended) and you do not need to have to use a SAN infrastructure either (NFS share can also be used). One thing I would recommend is a fencing device, on the HP server there is a network port on the back of each server called &#8220;ILO&#8221;.  This will allow the cluster software to power on, power off or restart a server remotely. Red Hat cluster package allow you to use a lot of [similar fencing devices](https://access.redhat.com/kb/docs/DOC-30004). This part of the cluster is important because this will prevent at one point some nodes in the cluster to write to a non-shareable file-system and create data corruption. If you do not have a [fencing](http://sources.redhat.com/cluster/wiki/Fence) device, you can always use the manual [fencing method](http://docs.redhat.com/docs/en-US/Red_Hat_Enterprise_Linux/3/html/GFS_6.0_Administration_Guide/s1-fence-methods.html) it works, but it not supported.

### The Setup

Our cluster will be contains 3 servers, we will have 2 actives servers and one passive. For our example, we will have one server running an HTTP service, the second server will be an FTP server. The third server will be use as a fail-over server, if the first or second server have network, SAN, or hardware problem, the service it is running will move the third server.

Although not required, having a passive server offer some advantage, first it make sure that if one server have problem the passive server will be able to handle the load of any of your server. If we did  not have that passive server we would need to make sure that either of the server would be capable of handling the load of the two servers on one server.

Clustering environment offer some other advantage when time come to do hardware update on a server. Let say we need to add memory to the first server, we could move the HTTP service from one server to the passive node, add memory to the first server and then move back to the service on the original node when ready. Another advantage of having a passive server is that you can update the OS of your node one by one without affecting the service (if reboot is necessary).

So the name of our node will be &#8220;bilbo&#8221; the will host the http service, &#8220;gollum&#8221; the will host the FTP service and &#8220;gandalf&#8221; will be our passive node.

<p style="text-align: center;">
  <a href="http://192.168.1.88/wp-content/uploads/2011/03/cluster.png"><img loading="lazy" class="size-medium wp-image-1690 aligncenter" title="Cluster Network" src="http://192.168.1.88/wp-content/uploads/2011/03/cluster-300x143.png" alt="" width="300" height="143" /></a>
</p>

<p style="text-align: center;">
  Click on image to view it enlarge.
</p>

<p style="text-align: left;">
  As you can see above, each server use 3 network cards.
</p>

<p style="text-align: left;">
  <!--more-->
</p>

<p style="text-align: left;">
  The first NIC (eth0), is use to offer the service HTTP and FTP to the users and it is the host main network card.
</p>

<p style="text-align: left;">
  The second network card (eth1) is used by cluster software for his heartbeat. In a corporate environment, this network should be isolated from the rest of your network. There will be a lot of  broadcast on it and the response time is important.
</p>

<p style="text-align: left;">
  The ILO network will be used by the cluster software to remotely poweron/poweroff server in our cluster, when a problem arise.
</p>

<p style="text-align: left;">
  So out /etc/hosts file look like this :
</p>

<pre>#
127.0.0.1       localhost.localdomain   localhost
#
# Host Real IP
192.168.1.103   gandalf.maison.ca       gandalf
192.168.1.104   gollum.maison.ca        gollum
192.168.1.111   bilbo.maison.ca         bilbo
#
# Service Virtual IP
192.168.1.204   ftp.maison.ca           ftp
192.168.1.211   www.maison.ca           www
#
# HeartBeat IP Address
10.10.10.103    hbgandalf.maison.ca     hbgandalf
10.10.10.104    hbgollum.maison.ca      hbgollum
10.10.10.111    hbbilbo.maison.ca       hbbilbo
#
# HP ILO IP Address
192.168.1.3     ilo_gandalf.maison.ca   ilo_gandalf
192.168.1.4     ilo_gollum.maison.ca    ilo_gollum
192.168.1.11    ilo_bilbo.maison.ca     ilo_bilbo
#</pre>

<p style="text-align: left;">
  &nbsp;
</p>

<p style="text-align: left;">
  We should define all the ip address that our cluster need to function properly in your /etc/hosts. Of course they should also be define in your DNS, but in case your DNS does not respond, the /etc/hosts will assure you that the cluster will continue to work properly. We will use the domain name &#8220;maison.ca¨ through out these articles.
</p>

<p style="text-align: left;">
  In our <a title="Second Article" href="http://linternux.com/2011/01/creating-a-redhatcentos-cluster-part-2/" target="_blank" rel="noopener">second article</a>, we will be installing the cluster software and begin the configuration of the cluster.
</p>

<p style="text-align: left;">
  &nbsp;
</p>

<p style="text-align: left;">
  &nbsp;
</p>

&nbsp;

<a title="Part 1 - Creating a RedHat/CentOS Cluster" href="http://linternux.com/2010/11/creating-cluster-part-1/" target="_blank" rel="noopener">Part 1 &#8211; Creating a Linux ReadHat/CentOS cluster</a>

<a href="http://linternux.com/2011/01/creating-a-redhatcentos-cluster-part-2/" target="_blank" rel="noopener">Part 2 &#8211; Creating a Linux ReadHat/CentOS cluster</a>

<a title="Part 3 of Creating a RedHat/CentOS cluster" href="http://linternux.com/2011/03/creating-a-red-hatcentos-cluster-part-3/" target="_blank" rel="noopener">Part 3 &#8211; Creating a Linux ReadHat/CentOS cluster</a>

<a title="Part 4 of Creating a RedHat/CentOS cluster" href="http://linternux.com/2011/04/creating-a-redhat-cluster-part-4/" target="_blank" rel="noopener">Part 4 &#8211; Creating a Linux ReadHat/CentOS cluster</a>

<a title="Part 5 of Creating a RedHat/CentOS cluster" href="http://linternux.com/2011/04/creating-a-redhat-cluster-part-5/" target="_blank" rel="noopener">Part 5 &#8211; Creating a Linux ReadHat/CentOS cluster</a>

&nbsp;