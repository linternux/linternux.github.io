---
id: 1454
title: 'Creating a Red Hat Cluster: Part 3'
date: 2011-03-19T09:04:33+00:00
author: jacques
layout: post
guid: http://linternux.com/?p=1454
permalink: /index.php/2011/03/19/creating-a-red-hatcentos-cluster-part-3/
aktt_notify_twitter:
  - 'yes'
aktt_tweeted:
  - "1"
categories:
  - Cluster
tags:
  - cluster
---
Here is the third of a series of article describing how to create a Linux Red Hat/CentOS cluster. At the end of this article, we will have a working cluster, all that will be left to do is the creation of the GFS filesystem and the scripts that will stop, start and give a status of our ftp and web services. You can refer to &#8220;<a title="Part 1 of Creating a RedHat cluster" href="http://linternux.com/2010/11/creating-cluster-part-1/" target="_blank" rel="noopener">Part1</a> , &#8220;<a title="Part 2 - Creating a RedHat/CentOS cluster" href="http://linternux.com/2011/01/creating-a-redhatcentos-cluster-part-2/" target="_blank" rel="noopener">Part 2</a>&#8221; and our [network diagram](http://192.168.1.88/wp-content/uploads/2011/03/cluster.png "Network Diagram of cluster") before reading this article, but for now let&#8217;s move on and continue our journey into building our cluster.

## Defining the fencing devices

[<img loading="lazy" class="size-medium wp-image-1455 alignleft" title="Create fencing device for bilbo.maison.ca" src="http://192.168.1.88/wp-content/uploads/2011/03/fence_device_bilbo-300x291.png" alt="" width="300" height="291" srcset="http://192.168.1.88/wp-content/uploads/2011/03/fence_device_bilbo-300x291.png 300w, http://192.168.1.88/wp-content/uploads/2011/03/fence_device_bilbo.png 726w" sizes="(max-width: 300px) 100vw, 300px" />](http://192.168.1.88/wp-content/uploads/2011/03/fence_device_bilbo.png)

The fencing device is use by the cluster software to power-off the node when it is considered in problem. We need to define a fencing device for all the nodes that we defined in the <a title="Part 2 of this article" href="http://linternux.com/2011/01/creating-a-redhatcentos-cluster-part-2/" target="_blank" rel="noopener">previous article</a>.

In our cluster, the fencing device used is the HP ILO interface. Select “the “HP ILO Device” from the pull down menu and enter the device information needed to connect to it. If you would like to see what are the fencing device supported by Red Hat cluster, you can consult the fencing FAQ on this <a title="Fencing device FAQ" href="http://sources.redhat.com/cluster/wiki/FAQ/Fencing" target="_blank" rel="noopener">page</a>.

You could use <a title="Manual fencing" href="http://sources.redhat.com/cluster/wiki/FAQ/Fencing#fence_manual3" target="_blank" rel="noopener">manual fence</a> for testing purpose but it is not supported in a production environment, since manual intervention is required to fence a server.

  * We choose to prefix the fencing device name with a lowercase “f_”, followed by the name assigned to its IP in <a title="/etc/hosts" href="http://192.168.1.88/wp-content/uploads/2011/03/cluster_hosts_file.png" target="_blank" rel="noopener">/etc/hosts</a>.
  * The login name to access the device.
  * The password used to authenticate to the device.
  * The host name as defined is our <a title="/etc/hosts file content" href="http://192.168.1.88/wp-content/uploads/2011/03/cluster_hosts_file.png" target="_blank" rel="noopener">host file</a> used to connect to the device.

&nbsp;

Repeat the operation for each server in the cluster.

[<img loading="lazy" class="size-full wp-image-1483 alignleft" title="Fencing Device for server &quot;gollum&quot;" src="http://192.168.1.88/wp-content/uploads/2011/03/fence_device_gollum1.png" alt="" width="258" height="261" />](http://192.168.1.88/wp-content/uploads/2011/03/fence_device_gollum1.png)

[<img loading="lazy" class="size-full wp-image-1457  alignleft" title="Fencing Device for server &quot;gandalf&quot;" src="http://192.168.1.88/wp-content/uploads/2011/03/fence_device_gandalf.png" alt="" width="258" height="261" />](http://192.168.1.88/wp-content/uploads/2011/03/fence_device_gandalf.png)

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

<!--more-->

## Defining the fence level

Next, we need to associate a fencing device with each server in our cluster. Let&#8217;s begin by clicking on the node name &#8220;hbbilbo.maison.ca&#8221;, then press on the button name (“Manage Fencing For This Node”) at the bottom right of the screen. You will be presented with a similar screen than the one below.

[<img loading="lazy" class="size-medium wp-image-1459 alignleft" title="Defining fencing for bilbo.maison.ca" src="http://192.168.1.88/wp-content/uploads/2011/03/fencing_bilbo-300x291.png" alt="" width="300" height="291" />](http://192.168.1.88/wp-content/uploads/2011/03/fencing_bilbo.png)

&nbsp;

&nbsp;

Now click on the &#8220;Add a New Fence Level&#8221; button, this will create a new fence level named &#8220;Fence_Level-1&#8221;.

Next, select &#8220;Fence-Level-1&#8221; on the left of the screen and then click on the button &#8220;Add a New Fence to this Level&#8221; . A little pop-up will appear, allowing you to assign the  fencing device to the host name. In our case we select the fencing device name &#8220;f_bilbo&#8221;. So we just created an association between a fencing device and a node.

&nbsp;

&nbsp;

&nbsp;

[<img loading="lazy" class="size-medium wp-image-1462  alignleft" title="Adding a fence level " src="http://192.168.1.88/wp-content/uploads/2011/03/fencing_bilbo_2-300x259.png" alt="" width="270" height="233" srcset="http://192.168.1.88/wp-content/uploads/2011/03/fencing_bilbo_2-300x259.png 300w, http://192.168.1.88/wp-content/uploads/2011/03/fencing_bilbo_2.png 502w" sizes="(max-width: 270px) 100vw, 270px" />](http://192.168.1.88/wp-content/uploads/2011/03/fencing_bilbo_2.png)

[<img loading="lazy" class="size-medium wp-image-1471  alignleft" title="Assigning fence level to node" src="http://192.168.1.88/wp-content/uploads/2011/03/fencing_bilbo_3-300x262.png" alt="" width="270" height="236" srcset="http://192.168.1.88/wp-content/uploads/2011/03/fencing_bilbo_3-300x262.png 300w, http://192.168.1.88/wp-content/uploads/2011/03/fencing_bilbo_3.png 501w" sizes="(max-width: 270px) 100vw, 270px" />](http://192.168.1.88/wp-content/uploads/2011/03/fencing_bilbo_3.png)

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

Click on the &#8220;OK&#8221;  button to close the screen above and then press the &#8220;Close&#8221;  button.

Back on this screen, press the &#8220;Close&#8221; button again to end the definition of our fence level.

&nbsp;

[<img loading="lazy" class="size-medium wp-image-1472  alignleft" title="Fence level assigned to hbbilbo node" src="http://192.168.1.88/wp-content/uploads/2011/03/fencinv_bilbo_4-300x260.png" alt="" width="300" height="260" srcset="http://192.168.1.88/wp-content/uploads/2011/03/fencinv_bilbo_4-300x260.png 300w, http://192.168.1.88/wp-content/uploads/2011/03/fencinv_bilbo_4.png 500w" sizes="(max-width: 300px) 100vw, 300px" />](http://192.168.1.88/wp-content/uploads/2011/03/fencinv_bilbo_4.png)

We need to repeat the operation for each node in our cluster.

Once a fence level is created for all nodes, you can proceed with the next step.

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

## Define Failover Domain

A failover domain is a named subset of cluster nodes that are eligible to run a cluster service in the event of a node failure. The name of our failover domain, will always begin with “fd__”_ (lowercase), this is the standard that I choose.

[<img loading="lazy" class="size-medium wp-image-1505  alignleft" title="Creating a failover Domain" src="http://192.168.1.88/wp-content/uploads/2011/03/FailDomain_1-300x292.png" alt="" width="300" height="292" srcset="http://192.168.1.88/wp-content/uploads/2011/03/FailDomain_1-300x292.png 300w, http://192.168.1.88/wp-content/uploads/2011/03/FailDomain_1.png 725w" sizes="(max-width: 300px) 100vw, 300px" />](http://192.168.1.88/wp-content/uploads/2011/03/FailDomain_1.png)

So let&#8217;s begin defining our failover domain, by clicking on &#8220;Failover Domains&#8221; on the right side of the GUI and pressing the &#8220;Create a Failover Domain&#8221; button.

Now enter the name of the failover domain, in this case for &#8220;bilbo&#8221; server will be &#8220;fd_bilbo&#8221; to stick to our standard.  Press the &#8220;OK&#8221; button to proceed.

[<img loading="lazy" class="size-medium wp-image-1506 alignleft" title="Defining hbbilbo.maison.ca failover Domain" src="http://192.168.1.88/wp-content/uploads/2011/03/FailDomain_2-300x262.png" alt="" width="300" height="262" srcset="http://192.168.1.88/wp-content/uploads/2011/03/FailDomain_2-300x262.png 300w, http://192.168.1.88/wp-content/uploads/2011/03/FailDomain_2.png 397w" sizes="(max-width: 300px) 100vw, 300px" />](http://192.168.1.88/wp-content/uploads/2011/03/FailDomain_2.png)

[<img loading="lazy" class="size-medium wp-image-1507 alignleft" title="Defining where bilbo service are allow to be move" src="http://192.168.1.88/wp-content/uploads/2011/03/failDomain_3-300x259.png" alt="" width="300" height="259" />](http://192.168.1.88/wp-content/uploads/2011/03/failDomain_3.png)

The failover domain configuration, list all the servers that will be part of the specified failover domain. We choose, that each member of the cluster will be listed in our failover domain.

So **_select each server one at a time from the selection list &#8220;Available Cluster Nodes&#8221;_** , until they have all been selected.  When all the servers are selected, the selection list will display &#8220;No Cluster Nodes Available&#8221;.

We choose to **_restrict the failover domain_** to the list of servers we have included. This means all of them for this cluster, but they can be situation were you could have six nodes in a cluster and we want one service to run on only three of the servers because they have more cpu and memory then the three others .

We also want the service to be prioritized, **check the “Prioritized List” checkbox**.

[<img loading="lazy" class="size-medium wp-image-1508 alignleft" title="Gandalf Failover Domain" src="http://192.168.1.88/wp-content/uploads/2011/03/failDomain_4-300x261.png" alt="" width="300" height="261" srcset="http://192.168.1.88/wp-content/uploads/2011/03/failDomain_4-300x261.png 300w, http://192.168.1.88/wp-content/uploads/2011/03/failDomain_4.png 396w" sizes="(max-width: 300px) 100vw, 300px" />](http://192.168.1.88/wp-content/uploads/2011/03/failDomain_4.png)

[<img loading="lazy" class="size-medium wp-image-1509 alignleft" title="Failover Domain for gollum server" src="http://192.168.1.88/wp-content/uploads/2011/03/failDomain_5-300x261.png" alt="" width="300" height="261" />](http://192.168.1.88/wp-content/uploads/2011/03/failDomain_5.png)

You may then highlight the nodes listed in the “Member Node” box and click the “Adjust Priority”  buttons to select the order in which the failover should occur. The node with the highest priority (priority 1) will be the initial node for running the service. If that node fails, the service will be relocated to the node with the next higher priority (priority 2) and so forth.

So for the failover domain  &#8220;fd_bilbo&#8221;  the &#8220;hbbilbo&#8221;  will be prioritize (1), if the server is power-off then the service will be move to &#8220;hbgandalf&#8221; (2) our passive node and if it unavailable then service will move to the server &#8220;hbgollum&#8221; (3).

The preferred server for the failover domain &#8220;fd_gollum&#8221; will be &#8220;hbgollum&#8221; (1), if it is not available then our passive server &#8220;hbgandalf&#8221;  (2) will take over. And in the eventuality that the two servers are not available then &#8220;hbbilbo&#8221; (3) will take over.

For the failover domain &#8220;fd_gandalf&#8221; we have choosen to give &#8220;hbgandalf&#8221;  priority one, &#8220;hbgollum&#8221; priority two and three to &#8220;hbbilbo&#8221; .

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

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

## Defining cluster resources

Cluster resource can be IP addresses, scripts, NFS and/or GFS filesystem mount point, database that are needed to run a service. In this case our services will be a FTP and a web site.

[<img loading="lazy" class="size-medium wp-image-1523 alignleft" title="Defining a new IP resource" src="http://192.168.1.88/wp-content/uploads/2011/03/Ressource_IP1-300x292.png" alt="" width="300" height="292" srcset="http://192.168.1.88/wp-content/uploads/2011/03/Ressource_IP1-300x292.png 300w, http://192.168.1.88/wp-content/uploads/2011/03/Ressource_IP1.png 725w" sizes="(max-width: 300px) 100vw, 300px" />](http://192.168.1.88/wp-content/uploads/2011/03/Ressource_IP1.png)

If you remember inl our [cluster network diagram](http://192.168.1.88/wp-content/uploads/2011/03/cluster.png), we&#8217;ll be running 2 services in our cluster. The first service will be a FTP server name &#8220;ftp.maison.ca&#8221; running at IP 192.168.1.204 and a web service &#8220;www.maison.ca&#8221; running at IP 192.168.2.211.

So let&#8217;s define an &#8220;IP Resource&#8221; for our ftp server at 192.168.1.204. Click on &#8220;Resources&#8221; on the left part of the screen and then click on the button &#8220;Create a Resource&#8221;  at the lower right of the screen. From the drop down list, select the &#8220;IP Address&#8221; resource type.

[<img loading="lazy" class="size-medium wp-image-1539 alignleft" title="Defining our IP Resource for ftp service" src="http://192.168.1.88/wp-content/uploads/2011/03/Ressource_IP21-300x292.png" alt="" width="300" height="292" srcset="http://192.168.1.88/wp-content/uploads/2011/03/Ressource_IP21-300x292.png 300w, http://192.168.1.88/wp-content/uploads/2011/03/Ressource_IP21.png 725w" sizes="(max-width: 300px) 100vw, 300px" />](http://192.168.1.88/wp-content/uploads/2011/03/Ressource_IP21.png)

Enter the IP of our ftp server &#8220;192.168.1.204&#8221; and make sure that the &#8220;Monitor Link&#8221; check box is selected. That means that if for a reason or another this IP is no longer responding, then this will trigger the move of our ftp service to the next server that we have defined in our failover domain for that node.

Repeat the process for our web site at IP 192.168.1.211.

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

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

## Defining our cluster services

We have decided that we would be running two services in our cluster, one for the ftp and one for the web site.

[<img loading="lazy" class="size-medium wp-image-1543 alignleft" title="Creating our ftp service" src="http://192.168.1.88/wp-content/uploads/2011/03/service_01-300x291.png" alt="" width="300" height="291" />](http://192.168.1.88/wp-content/uploads/2011/03/service_01.png)

Now is the time to define these services, we will begin by creating our ftp service. Click on &#8220;Services&#8221; on the bottom left of the screen and then press the button called &#8220;Create a Service&#8221; to begin the process.

First, we need to assign a name to our service, we choose to prefix our service by &#8220;srv\_&#8221; , this will be our standard. We choose a short name, because they are nicer when they are displayed by the &#8220;clustat&#8221; command that we will see later on. So enter the service name &#8220;srv\_ftp&#8221; and click on the &#8220;OK&#8221; button.

[<img loading="lazy" class="size-medium wp-image-1544 alignleft" title="Assign our ftp service to a failover domain fd_gollum" src="http://192.168.1.88/wp-content/uploads/2011/03/Service_02-300x275.png" alt="" width="300" height="275" srcset="http://192.168.1.88/wp-content/uploads/2011/03/Service_02-300x275.png 300w, http://192.168.1.88/wp-content/uploads/2011/03/Service_02.png 672w" sizes="(max-width: 300px) 100vw, 300px" />](http://192.168.1.88/wp-content/uploads/2011/03/Service_02.png)

Next you will see a screen similar to this one.

First we need to **assign a failover domain** to our ftp service. As planned on our <a title="Cluster Network Diagram" href="http://192.168.1.88/wp-content/uploads/2011/02/cluster.png" target="_blank" rel="noopener">cluster network diagram</a> we were going to run the ftp service on the gollum server, so from the failover domain drop down list we select the failover domain &#8220;fd_gollum&#8221; for our ftp service.

Next we need to make our FTP server IP (192.168.1.204)  part of the service. To do so **click on the button &#8220;Add a Shared Resource to this service&#8221;** and select the IP used for our ftp service.

[<img loading="lazy" class="size-full wp-image-1545 alignleft" src="http://192.168.1.88/wp-content/uploads/2011/03/Service_03.png" alt="" width="143" height="74" />](http://192.168.1.88/wp-content/uploads/2011/03/Service_03.png)

&nbsp;

&nbsp;

&nbsp;

[<img loading="lazy" class="alignleft size-full wp-image-1546" title="Select our FTP service IP" src="http://192.168.1.88/wp-content/uploads/2011/03/Service_04.png" alt="" width="282" height="260" />](http://192.168.1.88/wp-content/uploads/2011/03/Service_04.png)

After pressing the button above,  you should see a screen similar to this one.

Click on the IP that our ftp server will use (192.168.2.204) and press the &#8220;OK&#8221; button.

[<img loading="lazy" class="size-medium wp-image-1547  alignleft" title="Include the FTP IP into the FTP service" src="http://192.168.1.88/wp-content/uploads/2011/03/Service_05-300x263.png" alt="" width="300" height="263" srcset="http://192.168.1.88/wp-content/uploads/2011/03/Service_05-300x263.png 300w, http://192.168.1.88/wp-content/uploads/2011/03/Service_05.png 562w" sizes="(max-width: 300px) 100vw, 300px" />](http://192.168.1.88/wp-content/uploads/2011/03/Service_05.png)

One last thing before finishing the definition of our ftp service, **make sure you select &#8220;Relocate&#8221; from the &#8220;Recovery Policy&#8221;**. This allow our service to be move automatically to another server if the FTP service become inaccessible.

We can now press the &#8220;Close&#8221; button and repeat the operation to create of our web service.

Follow the name procedure as the ftp service to create the web service.

The name that we decided to call the service for the web site is &#8220;srv_www&#8221; .

[<img loading="lazy" class="size-full wp-image-1548" title="The name of our web service" src="http://192.168.1.88/wp-content/uploads/2011/03/Service_06.png" alt="" width="241" height="126" />](http://192.168.1.88/wp-content/uploads/2011/03/Service_06.png)

[<img loading="lazy" class="size-medium wp-image-1549 alignleft" title="Add of web IP to the service" src="http://192.168.1.88/wp-content/uploads/2011/03/Service_07-300x262.png" alt="" width="300" height="262" srcset="http://192.168.1.88/wp-content/uploads/2011/03/Service_07-300x262.png 300w, http://192.168.1.88/wp-content/uploads/2011/03/Service_07.png 561w" sizes="(max-width: 300px) 100vw, 300px" />](http://192.168.1.88/wp-content/uploads/2011/03/Service_07.png)

The web site service &#8220;srv\_www&#8221; will be part of the &#8220;fb\_bilbo&#8221; failover domain, so it will start on the &#8220;bilbo&#8221; server.

If the web service become unavailable, the service will be relocated to another server within the cluster. The server to where the web service will be move, is be based on the priority order we have put in the failover domain. So the IP will automatically be move when the service is relocated to another server within the cluster. We will demonstrate that later on in this article.

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

## Propagate cluster configuration

We should have now a working cluster, it does not have the final configuration but we will be able to bring it up and move services from one node to another. The missing part of our cluster in order to finalize it, is the creation of the GFS filesystem and our scripts that will bring up and down our ftp/web site. We will look at that in the next article.

[<img loading="lazy" class="size-medium wp-image-1550 alignleft" title="Propagate our cluster definition" src="http://192.168.1.88/wp-content/uploads/2011/03/Propagate_config-300x260.png" alt="" width="300" height="260" />](http://192.168.1.88/wp-content/uploads/2011/03/Propagate_config.png)

But for now let&#8217;s push this configuration to our cluster, by pressing the &#8220;Sent to Cluster&#8221; button. This will copy our new configuration file &#8220;/etc/cluster/cluster.conf&#8221; to every member of the cluster and activate it.

We will need to confirm our intention, by pressing the &#8220;Yes&#8221; button to the pop up below.

[<img loading="lazy" class="size-medium wp-image-1551  alignleft" title="Saving and propagate cluster configuration" src="http://192.168.1.88/wp-content/uploads/2011/03/Propagate_02-300x106.png" alt="" width="300" height="106" />](http://192.168.1.88/wp-content/uploads/2011/03/Propagate_02.png)

[<img loading="lazy" class="size-medium wp-image-1552 alignleft" title="Success config activated on all nodes" src="http://192.168.1.88/wp-content/uploads/2011/03/Propagate_03-300x125.png" alt="" width="300" height="125" srcset="http://192.168.1.88/wp-content/uploads/2011/03/Propagate_03-300x125.png 300w, http://192.168.1.88/wp-content/uploads/2011/03/Propagate_03.png 388w" sizes="(max-width: 300px) 100vw, 300px" />](http://192.168.1.88/wp-content/uploads/2011/03/Propagate_03.png)

I had some problem pushing the initial configuration to other member of the cluster, on my first attempt. If this happen, you may have to copy it manually. The cluster configuration file is stored in /etc/cluster and is named cluster.conf.  If you have ran the &#8220;system-config-cluster&#8221; GUI on &#8220;bilbo&#8221; server, then issue the following command on &#8220;bilbo&#8221; to copy the configuration file to the other servers ;

> **scp /etc/cluster/cluster.conf  gandalf:/etc/cluster**
> 
> **scp /etc/cluster/cluster.conf  gollum:/etc/cluster**

If the copy is done manually, you will have to restart the cluster services on each node or reboot all the nodes. Once you have a working cluster, you will not have to manually copy the cluster configuration file, pressing the button &#8220;Send to Cluster&#8221; will be enough. You can also the command &#8220;ccs_tools&#8221; to propagate the cluster configuration change (describe below).

&nbsp;

&nbsp;

&nbsp;

&nbsp;

## Manual adjustment to cluster configuration file

I made the following changes to the cluster configuration file to prevent the reboot of the server after it is fenced (For the HP ILO). When a server is fenced, the service is transferred to another node and the server that was hosting that service is power off (fenced). If we do not make the following change, the server that was power off will automatically reboot and perhaps then same error condition may occur (network, switch, FC problem), it would power off / power on, / power off / power on …. We want to eliminate that.

Every time you update the cluster configuration file manually you need to increment the configuration version number, so it will trigger an update on the others servers .

> root@gandalf:~# vi /etc/cluster/cluster..conf

&nbsp;

**<span style="color: #800000;">Increment the version number</span>**

Since we are changing the configuration file**,** need to increment the version number.**  
** 

**Before change** 

> <cluster alias=&#8221;our\_cluster&#8221; config\_version=&#8221;**<span style="color: #0000ff;">74</span>**&#8221; name=&#8221;our_cluster&#8221;>

**After the change**

> <cluster alias=&#8221;our\_cluster&#8221; config\_version=&#8221;**<span style="color: #0000ff;">75</span>**&#8221; name=&#8221;our_cluster&#8221;>

&nbsp;

**<span style="color: #800000;">Changes to prevent rebooting of the node after a power-off</span>  
** 

We need to do this modification for each of the &#8220;clusternode name&#8221; section. This will prevent a restart of the node after it has been fenced.

**Before the change**

> <clusternode name=&#8221;hbbilbo.maison.ca&#8221; nodeid=&#8221;1&#8243; votes=&#8221;1&#8243;>  
> <fence>  
> <method name=&#8221;1&#8243;>  
> **<span style="color: #0000ff;"><device name=&#8221;f_bilbo&#8221;/></span>**  
> </method>  
> </fence>  
> </clusternode>

**After the change**

> <clusternode name=&#8221;hbbilbo.maison.ca&#8221; nodeid=&#8221;1&#8243; votes=&#8221;1&#8243;>  
> <fence>  
> <method name=&#8221;1&#8243;>  
> <span style="color: #0000ff;"><strong><device </strong><strong>action=&#8221;off&#8221; </strong><strong>name=&#8221;f_bilbo&#8221;/></strong></span>  
> </method>  
> </fence>  
> </clusternode>

&nbsp;

<span style="color: #800000;"><strong>Increase resource manager verbosity</strong></span>

I include here something optional, if you are having some problem with your cluster, increasing verbosity of the resource manager daemon &#8220;rgmanager&#8221; may help you.  Changing the <rm> line will add more debugging information in the /var/log/rgmanager file (syslogd need to be restarted).

**Before the change**

> </fencedevices>  
> **<span style="color: #000080;"><rm></span>**  
> <failoverdomains>

**After the change**

> </fencedevices>  
> <span style="color: #0000ff;"><strong><rm log_facility=&#8221;local4&#8243; log_level=&#8221;7&#8243;></strong></span>  
> <failoverdomains>

Whenever you update the configuration file manually you can use the “**ccs_tool**” command to propagate the new cluster configuration file to all cluster members. Don&#8217;t forget to update the version number.<figure id="attachment_1592" aria-describedby="caption-attachment-1592" style="width: 300px" class="wp-caption aligncenter">

[<img loading="lazy" class="size-medium wp-image-1592 " title="Click to enlarge" src="http://192.168.1.88/wp-content/uploads/2011/03/ccs_tool-300x60.png" alt="" width="300" height="60" srcset="http://192.168.1.88/wp-content/uploads/2011/03/ccs_tool-300x60.png 300w, http://192.168.1.88/wp-content/uploads/2011/03/ccs_tool.png 489w" sizes="(max-width: 300px) 100vw, 300px" />](http://192.168.1.88/wp-content/uploads/2011/03/ccs_tool.png)<figcaption id="caption-attachment-1592" class="wp-caption-text">Distribute new cluster config file</figcaption></figure> 

&nbsp;

## Checking cluster functionality

We can check the status of your cluster in two different ways. The simplest one is to use the &#8220;clustat&#8221; command (see output below).  If you want a continuous display, you can add &#8220;-i 2&#8221; to the &#8220;clustat&#8221; command, to have the output refreshed every two seconds (Press CTRL-C to stop the display).<figure id="attachment_1602" aria-describedby="caption-attachment-1602" style="width: 300px" class="wp-caption alignleft">

[<img loading="lazy" class="size-medium wp-image-1602" title="click to enlarge" src="http://192.168.1.88/wp-content/uploads/2011/03/clustat-300x96.png" alt="" width="300" height="96" srcset="http://192.168.1.88/wp-content/uploads/2011/03/clustat-300x96.png 300w, http://192.168.1.88/wp-content/uploads/2011/03/clustat-768x246.png 768w, http://192.168.1.88/wp-content/uploads/2011/03/clustat.png 999w" sizes="(max-width: 300px) 100vw, 300px" />](http://192.168.1.88/wp-content/uploads/2011/03/clustat.png)<figcaption id="caption-attachment-1602" class="wp-caption-text">clustat command output</figcaption></figure> 

This is the normal output you should have. All our members are &#8220;Online&#8221;  with the resource manager running on them.

The name of each members of the cluster, their &#8220;Node ID&#8221; and their status are displayed.

Our two services &#8220;srv\_ftp&#8221; and &#8220;srv\_www&#8221;  are running (started) on the selected server.<figure id="attachment_1603" aria-describedby="caption-attachment-1603" style="width: 300px" class="wp-caption alignleft">

[<img loading="lazy" class="size-medium wp-image-1603" title="Click top enlarge" src="http://192.168.1.88/wp-content/uploads/2011/03/clustat_GUI-300x203.png" alt="" width="300" height="203" srcset="http://192.168.1.88/wp-content/uploads/2011/03/clustat_GUI-300x203.png 300w, http://192.168.1.88/wp-content/uploads/2011/03/clustat_GUI.png 721w" sizes="(max-width: 300px) 100vw, 300px" />](http://192.168.1.88/wp-content/uploads/2011/03/clustat_GUI.png)<figcaption id="caption-attachment-1603" class="wp-caption-text">system-config-cluster (cluster mgmt tab)</figcaption></figure> 

The second way to get the cluster status, is to run the cluster GUI (system-config-cluster) and then click on the &#8220;Cluster Management&#8221; tab.

In the upper part of the screen, we can see the current member of our cluster and their &#8220;Node ID&#8221; .

Below, we can visualise the status of each services define within the cluster.

We can also see that our service &#8220;srv\_ftp&#8221;, is running (started) on &#8220;hbgollum&#8221; and that the service &#8220;srv\_www&#8221; is running on &#8220;hbbilbo.maison.ca&#8221; .

With the &#8220;Cluster Management&#8221; tab , we can disable (stop), enable (start) or restart each of these services. More on this later.

**Let&#8217;s check if our services IP are alive.**

The IP address of the ftp service that we have define is &#8220;192.168.1.204&#8221; and from the &#8220;clustat&#8221; command above we know that it is running on the server &#8220;gollum&#8221;. So let&#8217;s logon on that server and check if that IP is active. Be aware that the output of the &#8220;ifconfig -a&#8221;  does not include our FTP IP, we need to use the &#8220;ip&#8221; command to see it.  From the output below, we can see that server IP &#8220;192.168.1.104&#8221;  is active on &#8220;eth0&#8221; and that our ftp server IP  &#8220;192.168.1.204&#8221; is also active on the same interface. So our cluster software is doing it&#8217;s job, we have the &#8220;ftp.maison.ca&#8221; IP defined on the interface &#8220;eth0&#8221;. You will also notice that on &#8220;eth1&#8221;, we have our heartbeat IP &#8220;10.10.10.104&#8221; is active.

> **<span style="color: #0000ff;">root@gollum</span>**:~# **<span style="color: #0000ff;">ip addr list</span>**  
> **<span style="color: #0000ff;">1: lo</span>**: <LOOPBACK,UP,LOWER_UP> mtu 16436 qdisc noqueue  
> link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00  
> inet 127.0.0.1/8 scope host lo  
> inet6 ::1/128 scope host  
> valid\_lft forever preferred\_lft forever  
> **<span style="color: #0000ff;">2: eth0:</span>** <BROADCAST,MULTICAST,UP,LOWER\_UP> mtu 1500 qdisc pfifo\_fast qlen 1000  
> link/ether 00:50:da:68:df:9b brd ff:ff:ff:ff:ff:ff  
> inet **<span style="color: #0000ff;">192.168.1.104</span>**/24 brd 192.168.1.255 scope global eth0  
> inet **<span style="color: #0000ff;">192.168.1.204</span>**/24 scope global secondary eth0  
> inet6 fe80::250:daff:fe68:df9b/64 scope link  
> valid\_lft forever preferred\_lft forever  
> <span style="color: #0000ff;"><strong>3: eth1</strong></span>: <BROADCAST,MULTICAST,UP,LOWER\_UP> mtu 1500 qdisc pfifo\_fast qlen 1000  
> link/ether 00:02:a5:b1:a0:d4 brd ff:ff:ff:ff:ff:ff  
> inet **<span style="color: #0000ff;">10.10.10.104</span>**/24 brd 10.10.10.255 scope global eth1  
> inet6 fe80::202:a5ff:feb1:a0d4/64 scope link  
> valid\_lft forever preferred\_lft forever  
> **<span style="color: #0000ff;">4: sit0</span>**: <NOARP> mtu 1480 qdisc noop  
> link/sit 0.0.0.0 brd 0.0.0.0  
> root@gollum:~#

&nbsp;

On the server &#8220;bilbo&#8221; we have our web IP (192.168.1.211) active on the interface &#8220;eth0&#8221; along with the server IP (192.168.1.111). Our heartbeat IP is defined on the interface &#8220;eth1&#8221; .

> **<span style="color: #0000ff;">root@bilbo</span>**:~# **<span style="color: #0000ff;">ip addr list</span>**  
> **<span style="color: #0000ff;">1: lo:</span>** <LOOPBACK,UP,LOWER_UP> mtu 16436 qdisc noqueue  
> link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00  
> inet 127.0.0.1/8 scope host lo  
> inet6 ::1/128 scope host  
> valid\_lft forever preferred\_lft forever  
> **<span style="color: #0000ff;">2: eth0</span>**: <BROADCAST,MULTICAST,UP,LOWER\_UP> mtu 1500 qdisc pfifo\_fast qlen 1000  
> link/ether 00:01:02:75:80:58 brd ff:ff:ff:ff:ff:ff  
> inet **<span style="color: #0000ff;">192.168.1.111</span>**/24 brd 192.168.1.255 scope global eth0  
> inet **<span style="color: #0000ff;">192.168.1.21</span>1**/24 scope global secondary eth0  
> inet6 fe80::201:2ff:fe75:8058/64 scope link  
> valid\_lft forever preferred\_lft forever  
> **3: eth1:** <BROADCAST,MULTICAST,UP,LOWER\_UP> mtu 1500 qdisc pfifo\_fast qlen 1000  
> link/ether 00:50:8b:f4:c5:59 brd ff:ff:ff:ff:ff:ff  
> inet **<span style="color: #0000ff;">10.10.10.111</span>**/24 brd 10.10.10.255 scope global eth1  
> inet6 fe80::250:8bff:fef4:c559/64 scope link  
> valid\_lft forever preferred\_lft forever  
> **<span style="color: #0000ff;">4: sit0</span>**: <NOARP> mtu 1480 qdisc noop  
> link/sit 0.0.0.0 brd 0.0.0.0  
> root@bilbo:~#

I think we should stop the article here, we have now a working cluster that we will exploit in our next article.

In our next article, we will create our services scripts and our GFS filesystem so that our server can share the same filesystem. This feature will give us the ability to share information between server via a common filesystem. So when the service has to moved from one server to another, the service can start using the same data it was using on the previous server.

I don&#8217;t know if the next article will be the last one of this series of article on the cluster, but we will see.

Stay tune for part 4 of this series of article on creating a Linux Red Hat cluster.

<a title="Part 1 - Creating a RedHat/CentOS Cluster" href="http://linternux.com/2010/11/creating-cluster-part-1/" target="_blank" rel="noopener">Part 1 &#8211; Creating a Linux ReadHat/CentOS cluster</a>

<a href="http://linternux.com/2011/01/creating-a-redhatcentos-cluster-part-2/" target="_blank" rel="noopener">Part 2 &#8211; Creating a Linux ReadHat/CentOS cluster</a>

<a title="Part 3 of Creating a RedHat/CentOS cluster" href="http://linternux.com/2011/03/creating-a-red-hatcentos-cluster-part-3/" target="_blank" rel="noopener">Part 3 &#8211; Creating a Linux ReadHat/CentOS cluster</a>

<a title="Part 4 of Creating a RedHat/CentOS cluster" href="http://linternux.com/2011/04/creating-a-redhat-cluster-part-4/" target="_blank" rel="noopener">Part 4 &#8211; Creating a Linux ReadHat/CentOS cluster</a>

<a title="Part 5 of Creating a RedHat/CentOS cluster" href="http://linternux.com/2011/04/creating-a-redhat-cluster-part-5/" target="_blank" rel="noopener">Part 5 &#8211; Creating a Linux ReadHat/CentOS cluster</a>

&nbsp;