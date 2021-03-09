---
id: 1295
title: 'Creating a Red Hat Cluster: Part 2'
date: 2011-01-03T12:52:43+00:00
author: jacques
layout: post
guid: http://linternux.com/?p=1295
permalink: /index.php/2011/01/03/creating-a-redhatcentos-cluster-part-2/
aktt_notify_twitter:
  - 'yes'
aktt_tweeted:
  - "1"
categories:
  - Cluster
tags:
  - cluster
---
&nbsp;

Here is the second article on how to to build a Red Hat/CentOS cluster. The &#8220;<a title="Part one of this article" href="http://linternux.com/2010/11/creating-cluster-part-1/" target="_blank" rel="noopener">Part One of this articl</a>e&#8221; was posted a couple of month ago, I&#8217;m sorry for the delay but I have been a bit busy at work and at home.  I can promise that Part 3 will not take a couple of month to post. I want to terminate this series on the cluster and post some new ideas that I have.  In this article, we continue our journey on how-to build our cluster and stay tune for Part 3 coming soon.

## Installing the cluster software

If you are using Red Hat, you need to register your server in order to download new software or update. You also need to subscribe to the &#8220;Clustering&#8221; and &#8220;Cluster Storage&#8221; channel in order to install these groups of software. With CentOS, this is not needed since we can download these groups of software without registration. Let&#8217;s install the clustering software, by typing this command;

> <span style="color: #002060;"><span style="color: #000080;"><strong># yum groupinstall Clustering</strong></span><br /> </span>

Since we will be using GFS filesystem, we will need the &#8220;Cluster Storage&#8221; software group.

> <span style="color: #002060;"><span style="color: #000080;"><strong># yum groupinstall &#8220;Cluster Storage&#8221;</strong></span><br /> </span>

I also found out that this package is also needed by the cluster software, so let install it.

> <span style="color: #002060;"><span style="color: #000080;"># <strong>yum install perl-Crypt-SSLeay </strong></span><br /> </span>

If you are using a 32 bits kernel and you have more than 4 GB of memory on it, you need to install the PAE kernel and GFS modules. This will ensure that you are using all the memory available on the server.

> <span style="color: #002060;"><strong><span style="color: #000080;"># yum install kmod-gnbd-PAE kmod-gfs-PAE kernel-PA</span>E<br /> </strong></span>

Finally, let&#8217;s make sure that you have that the servers have the latest OS update.

> **<span style="color: #000080;"># yum –y update</span>  
>** 

## Setting the locking type for GFS filesystem

To use the <a title="RedHat GFS information page" href="http://www.redhat.com/gfs/" target="_blank" rel="noopener">GFS (Global File System)</a> with the cluster you need to activate the GFS locking in the /etc/lvm/lvm.conf file. We need to change the &#8220;locking_type&#8221; variable from 0 to 3, to inform LVM that we will be dealing iwth GFS volume group and GFS filesystem. This command needs to be <span style="color: blue;"><strong><em>run on all the servers</em></strong></span>.

<pre># grep -i locking_type /etc/lvm/lvm.conf
locking_type = 0
# <strong><span style="color: #0000ff;">lvmconf --enable-cluster</span></strong>
# grep -i locking_type /etc/lvm/lvm.conf
locking_type = 3
</pre>

<!--more-->

## Making sure SELinux and firewall are disable

We do not want to deal with SELinux and the firewall in our cluster, so we will disable them. From the gnome desktop run the following the following command ;

<pre><strong><span style="color: #000080;"># system-config-securitylevel</span></strong>
</pre><figure style="width: 258px" class="wp-caption alignleft">

<img loading="lazy" class="   " title="Disabling SELinux need a reboot to be activated" src="http://192.168.1.88/wp-content/uploads/2011/01/010311_1252_CreatingaRe13.png" alt="Disable SELinux" width="258" height="273" align="left" /> <figcaption class="wp-caption-text">Disabling SELinux need a reboot</figcaption></figure> <figure style="width: 258px" class="wp-caption alignleft"><img loading="lazy" class=" " title="Disable Firewall" src="http://192.168.1.88/wp-content/uploads/2011/01/010311_1252_CreatingaRe21.png" alt="Disable Firewall" width="258" height="272" align="left" /><figcaption class="wp-caption-text">Disable Firewall</figcaption></figure> 

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

## Activating cluster service

Let&#8217;s make sure that the cluster service are started each time the server is started.<figure id="attachment_1597" aria-describedby="caption-attachment-1597" style="width: 300px" class="wp-caption alignleft">

[<img loading="lazy" class="size-medium wp-image-1597 " title="Click to enlarge" src="http://192.168.1.88/wp-content/uploads/2011/01/activate_cluster_service-300x94.png" alt="" width="300" height="94" />](http://192.168.1.88/wp-content/uploads/2011/01/activate_cluster_service.png)<figcaption id="caption-attachment-1597" class="wp-caption-text">Activate cluster services</figcaption></figure> 

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

We now we can start the cluster services manually or reboot the server.<figure id="attachment_1599" aria-describedby="caption-attachment-1599" style="width: 300px" class="wp-caption alignleft">

[<img loading="lazy" class="size-medium wp-image-1599" title="start_cluster_services" src="http://192.168.1.88/wp-content/uploads/2011/01/start_cluster_services-300x134.png" alt="" width="300" height="134" />](http://192.168.1.88/wp-content/uploads/2011/01/start_cluster_services.png)<figcaption id="caption-attachment-1599" class="wp-caption-text">Start cluster services</figcaption></figure> 

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

## Starting cluster configuration

There are two tools you can use to configure and maintain your cluster. The first one is a web interface called &#8220;<a title="Article on Conga" href="http://magazine.redhat.com/2007/03/19/teaching-your-cluster-and-storage-systems-to-dance-an-introduction-to-conga/" target="_blank" rel="noopener">Conga</a>&#8221; that require the installation of a agent on each node (ricci) and a centralize configuration center named (lucy).  If you want to use the web interface (Seem to be working a lot better now),  it is advisable to install the configuration center on a Linux server outside of the cluster. This was fairly new when I first created my first cluster and we decided to used the second interface name &#8220;Cluster Configuration&#8221;.  This tool can be started from any node within the cluster and does not require to install any additional software. To start the cluster configuration GUI, type the following command using the &#8220;root&#8221; account ;<figure id="attachment_1385" aria-describedby="caption-attachment-1385" style="width: 300px" class="wp-caption alignleft">

[<img loading="lazy" class="size-medium wp-image-1385  " title="system-config-cluster" src="http://192.168.1.88/wp-content/uploads/2011/01/system-config-cluster1-300x54.png" alt="Start cluster configuration GUI from the command line" width="300" height="54" srcset="http://192.168.1.88/wp-content/uploads/2011/01/system-config-cluster1-300x54.png 300w, http://192.168.1.88/wp-content/uploads/2011/01/system-config-cluster1.png 333w" sizes="(max-width: 300px) 100vw, 300px" />](http://192.168.1.88/wp-content/uploads/2011/01/system-config-cluster1.png)<figcaption id="caption-attachment-1385" class="wp-caption-text">Start cluster GUI from the command line</figcaption></figure> 

<p style="margin-left: 36pt;">
  &nbsp;
</p>

<p style="margin-left: 36pt;">
  &nbsp;
</p>

<p style="margin-left: 36pt;">
  &nbsp;
</p>

<p style="margin-left: 36pt;">
  &nbsp;
</p><figure id="attachment_1396" aria-describedby="caption-attachment-1396" style="width: 270px" class="wp-caption alignleft">

[<img loading="lazy" class="size-medium wp-image-1396   " title="Click to enlarge" src="http://192.168.1.88/wp-content/uploads/2011/01/new_cluster_config-300x135.png" alt="new_cluster_config_warning" width="270" height="122" />](http://192.168.1.88/wp-content/uploads/2011/01/new_cluster_config.png)<figcaption id="caption-attachment-1396" class="wp-caption-text">new\_cluster\_config_warning</figcaption></figure> 

<p style="text-align: left;">
  &nbsp;
</p>

<p style="text-align: left;">
  The first time you run the cluster configuration GUI, a warning message may display.  It is just informing us that the cluster configuration file &#8220;/etc/cluster/cluster.conf&#8221; was not found. Click on the &#8220;Create New Configuration&#8221; button and let&#8217;s move on.
</p>

<p style="text-align: left;">
  &nbsp;
</p>

&nbsp;

&nbsp;<figure id="attachment_1403" aria-describedby="caption-attachment-1403" style="width: 144px" class="wp-caption alignleft">

[<img loading="lazy" class="size-medium wp-image-1403 " title="Cluster_GUI_MCast" src="http://192.168.1.88/wp-content/uploads/2011/01/Cluster_GUI_MCast-144x300.png" alt="Cluster general setting" width="144" height="300" srcset="http://192.168.1.88/wp-content/uploads/2011/01/Cluster_GUI_MCast-144x300.png 144w, http://192.168.1.88/wp-content/uploads/2011/01/Cluster_GUI_MCast.png 288w" sizes="(max-width: 144px) 100vw, 144px" />](http://192.168.1.88/wp-content/uploads/2011/01/Cluster_GUI_MCast.png)<figcaption id="caption-attachment-1403" class="wp-caption-text">Cluster general setting</figcaption></figure> 

&nbsp;

  * Next we need to enter the name of our cluster, we have choose to name it “our_cluster”.  The name of a cluster cannot be change. The only way to change the name of the cluster is to create a new one, so choose it wisely.
  * We use the recommend Lock Manager (Dynamic Lock Manager), the GULM is depreciated.
  * The multicast address we used for the heartbeat will be “239.1.1.1”.
  * The usage of the &#8220;Quorum Disk&#8221; is outside the scope of this article. But basically, you define a small disk on the SAN that is shared among the nodes in the cluster and node status are regularly written to that disk. If a node haven&#8217;t updated its status for a period of time, it will be considered down and the cluster will then fence that node. If you are interested to use the &#8220;Quorum Disk&#8221; there is an article <a title="Enhancing cluster quorum with QDisk" href="http://magazine.redhat.com/2007/12/19/enhancing-cluster-quorum-with-qdisk/" target="_blank" rel="noopener">here </a>that describe how to set it up.

&nbsp;

&nbsp;

&nbsp;

&nbsp;

## Cluster Properties<figure id="attachment_1409" aria-describedby="caption-attachment-1409" style="width: 300px" class="wp-caption alignleft">

[<img loading="lazy" class="size-medium wp-image-1409  " title="Click on the image to enlarge" src="http://192.168.1.88/wp-content/uploads/2011/01/Cluster_GUI_Properties-300x292.png" alt="Click on the image to enlarge" width="300" height="292" srcset="http://192.168.1.88/wp-content/uploads/2011/01/Cluster_GUI_Properties-300x292.png 300w, http://192.168.1.88/wp-content/uploads/2011/01/Cluster_GUI_Properties.png 726w" sizes="(max-width: 300px) 100vw, 300px" />](http://192.168.1.88/wp-content/uploads/2011/01/Cluster_GUI_Properties.png)<figcaption id="caption-attachment-1409" class="wp-caption-text">Cluster Properties</figcaption></figure> 

&nbsp;

Now let&#8217;s check some of the default setting given to our cluster. Click on “Cluster” on the left hand side of the screen and then click on the “Edit Cluster Properties”.

The **Configuration Version** value is by default to 1 and automatically incremented each time you modify your cluster configuration.

The **Post-Join Delay** parameter is the number of seconds the fence daemon (fenced) waits before fencing a node after the node joins the fence domain. The **Post-Join Delay** default value is **3**. A typical setting for **Post-Join Delay** is between 20 and 30 seconds, but can vary according to cluster and network performance.

The **Post-Fail Delay** parameter is the number of seconds the fence daemon (fenced) waits before fencing a node (a member of the fence domain) after the node has failed. The **Post-Fail Delay** default value is ****. Its value may be varied to suit cluster and network performance. **_CHANGE THE “POST FAIL DELAY” to 30 Seconds_**.

&nbsp;

&nbsp;

## Adding our nodes to cluster<figure id="attachment_1425" aria-describedby="caption-attachment-1425" style="width: 300px" class="wp-caption alignleft">

[<img loading="lazy" class="size-medium wp-image-1425 " title="Click on the image to enlarge" src="http://192.168.1.88/wp-content/uploads/2011/01/Cluster_GUI_Node_Bilbo-300x290.png" alt="" width="300" height="290" />](http://192.168.1.88/wp-content/uploads/2011/01/Cluster_GUI_Node_Bilbo.png)<figcaption id="caption-attachment-1425" class="wp-caption-text">Insert our first node "bilbo"</figcaption></figure> 

&nbsp;

To add node into the cluster let’s select “Cluster Nodes” on the upper left side of the screen and click on the “Add a Cluster Node” button. You will be then presented the “Node Properties” screen.  Enter the node name, the “Quorum Votes” and the name of the interface used for the “Multicast” (Heartbeat), in our case it is eth0. The avoid problem, I always used the first interface for the network cluster heartbeat.

  * Enter the name of the host name used for the heartbeat, for our first node it will be &#8220;hbbilbo.maison.ca. Remember that this name MUST be defined in![](file:///C:/Users/jacques/AppData/Local/Temp/moz-screenshot.png) <a title="View our /etc/hosts file" href="http://192.168.1.88/wp-content/uploads/2011/03/cluster_hosts_file.png" target="_blank" rel="noopener">our hosts file</a> and in your DNS (if you have one).

  * Quorum is a voting algorithm used by the cluster manager. We say a cluster has &#8216;quorum&#8217; if a majority of nodes are alive, communicating, and agree on the active cluster members. So in a thirteen-node cluster, quorum is only reached if seven or more nodes are communicating. If the seventh node dies, the cluster loses quorum and can no longer function. For our cluster we will **leave the Quorum Votes to the default value of 1**.

If we would have a two-node clusters, we would need to make a special exception to the quorum rules. There is a special setting &#8220;two\_node&#8221; in the /etc/cluster.conf file that looks like this:  <cman expected\_votes=&#8221;1&#8243; two_node=&#8221;1&#8243;/>

&nbsp;

Repeat operation for every node you want to include in the cluster<figure id="attachment_1365" aria-describedby="caption-attachment-1365" style="width: 270px" class="wp-caption alignleft">

[<img loading="lazy" class="size-medium wp-image-1365  " title="Click to enlarge" src="http://192.168.1.88/wp-content/uploads/2011/02/Cluster_GUI_Node_Gandalf-300x114.png" alt="Click to enlarge." width="270" height="103" srcset="http://192.168.1.88/wp-content/uploads/2011/02/Cluster_GUI_Node_Gandalf-300x114.png 300w, http://192.168.1.88/wp-content/uploads/2011/02/Cluster_GUI_Node_Gandalf.png 355w" sizes="(max-width: 270px) 100vw, 270px" />](http://192.168.1.88/wp-content/uploads/2011/02/Cluster_GUI_Node_Gandalf.png)<figcaption id="caption-attachment-1365" class="wp-caption-text">Insert "gandalf" node in our cluster</figcaption></figure> <figure id="attachment_1364" aria-describedby="caption-attachment-1364" style="width: 270px" class="wp-caption alignleft">[<img loading="lazy" class="size-medium wp-image-1364  " title="Click to enlarge" src="http://192.168.1.88/wp-content/uploads/2011/02/Cluster_GUI_Node_Gollum-300x114.png" alt="" width="270" height="103" srcset="http://192.168.1.88/wp-content/uploads/2011/02/Cluster_GUI_Node_Gollum-300x114.png 300w, http://192.168.1.88/wp-content/uploads/2011/02/Cluster_GUI_Node_Gollum.png 355w" sizes="(max-width: 270px) 100vw, 270px" />](http://192.168.1.88/wp-content/uploads/2011/02/Cluster_GUI_Node_Gollum.png)<figcaption id="caption-attachment-1364" class="wp-caption-text">Insert "gollum" node in our cluster</figcaption></figure> 

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

This article was getting quite long, so I decided to stop here, and continue in a another article.

We are almost there stay tune, the third one will be posted soon.

&nbsp;

<a title="Part 1 - Creating a RedHat/CentOS Cluster" href="http://linternux.com/2010/11/creating-cluster-part-1/" target="_blank" rel="noopener">Part 1 &#8211; Creating a Linux ReadHat/CentOS cluster</a>

<a href="http://linternux.com/2011/01/creating-a-redhatcentos-cluster-part-2/" target="_blank" rel="noopener">Part 2 &#8211; Creating a Linux ReadHat/CentOS cluster</a>

<a title="Part 3 of Creating a RedHat/CentOS cluster" href="http://linternux.com/2011/03/creating-a-red-hatcentos-cluster-part-3/" target="_blank" rel="noopener">Part 3 &#8211; Creating a Linux ReadHat/CentOS cluster</a>

<a title="Part 4 of Creating a RedHat/CentOS cluster" href="http://linternux.com/2011/04/creating-a-redhat-cluster-part-4/" target="_blank" rel="noopener">Part 4 &#8211; Creating a Linux ReadHat/CentOS cluster</a>

<a title="Part 5 of Creating a RedHat/CentOS cluster" href="http://linternux.com/2011/04/creating-a-redhat-cluster-part-5/" target="_blank" rel="noopener">Part 5 &#8211; Creating a Linux ReadHat/CentOS cluster</a>

&nbsp;