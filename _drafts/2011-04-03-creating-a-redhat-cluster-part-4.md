---
id: 1647
title: 'Creating a Red Hat Cluster: Part 4'
date: 2011-04-03T10:46:02+00:00
author: jacques
layout: post
guid: http://linternux.com/?p=1647
permalink: /index.php/2011/04/03/creating-a-redhat-cluster-part-4/
aktt_notify_twitter:
  - 'yes'
aktt_tweeted:
  - "1"
categories:
  - Cluster
tags:
  - cluster
---
Welcome back to LINternUX, where we continue the creation of our cluster. By now you should have a working cluster running an ftp service and a web service. Although the service are created, our ftp and web service are not really running yet. In this article we will create a GFS filesystem that will allows us to share data between nodes. In the next and last article we&#8217;ll finalise the cluster by completing our ftp and web service so they really work. We will also show you how to manually move service from one server to another. So we still have some work to do, so let&#8217;s start right away.

&nbsp;

## Adding a SAN disk to our servers

The Linux operating system is installed on the internal disks for each of our server. We will now add a SAN disk that will be visible be each of our server. I assume here that your SAN and your Brocade switch are configure accordingly. Explaining how to set up the SAN and the Brocade switch is not in the scope of this article. But I think that you get the idea that the new disk must be visible by every node in our cluster. In the example below we already have a SAN disk (sda) with one partition (sda1) on it. Adding a disk to the server, can be done (live) without any interruption of service, if you follow the steps below. I would suggest you practice on a test server, to become familiar with the procedure.

Before we add a disk, let’s see what are the visible disks on the system, by looking at the /proc/partitions file. We can see that we already have a disk (sda) with one partition on it. So the new disk that we&#8217;re going to add, should be seen as &#8220;sdb&#8221;.

> root@gollum~# <span style="color: #0000ff;"><strong>grep sd /proc/partitions</strong></span>  
> 8     0  104857920 sda  
> 8     1  104856223 sda1

Let&#8217;s rescan the SCSI bus by typing the command below. This command must be run on each of the server within the cluster. Here, we have only one HBA (Host Base Adapter)  card connected to the SAN on each server. If you have a second HBA, you need to run the same command for the second HBA, but replace the &#8220;host0&#8221; by &#8220;host1&#8221;.

> root@gollum~#  **<span style="color: #0000ff;">echo &#8220;- &#8211; -&#8221; > /sys/class/scsi_host/host0/scan</span>**  
> root@gandalf~# ** <span style="color: #0000ff;">echo &#8220;- &#8211; -&#8221; > /sys/class/scsi_host/host0/scan</span>**  
> root@bilbo~#     <span style="color: #0000ff;"><strong>echo &#8220;- &#8211; -&#8221; > /sys/class/scsi_host/host0/scan</strong></span>

Let’s see if we have some new disk(s) that were detected (sdb) (check each servers)

> root@gollum~# <span style="color: #0000ff;"><strong>grep sd /proc/partitions</strong></span>  
> 8     0  104857920 sda  
> 8     1  104856223 sda1  
> <span style="color: #0000ff;">8    16   15728640 sdb</span>

<!--more-->

Let’s create a LVM partition on our new disk (sdb) by running the &#8220;fdisk&#8221; command..

> \# **<span style="color: #0000ff;">fdisk /dev/sdb</span>**
> 
> Command (m for help): p
> 
> Disk /dev/sdh: 107.3 GB, 107374510080 bytes  
> 255 heads, 63 sectors/track, 13054 cylinders  
> Units = cylinders of 16065 * 512 = 8225280 bytes  
> Device Boot      Start         End      Blocks   Id  System
> 
> Command (m for help): **n** <span style="color: #0000ff;">(n=new partition)</span>  
> Command action  
> e   extended  
> p   primary partition (1-4) **p** <span style="color: #0000ff;">(p=primary partition)</span>  
> Partition number (1-4): **1** <span style="color: #0000ff;">(first partition =1)</span>  
> First cylinder (1-13054, default 1):  **1**  <span style="color: #0000ff;">(Start at the beginning of disk)</span>  
> Last cylinder or +size or +sizeM or +sizeK (1-13054, default 13054): **13054** <span style="color: #0000ff;">(End of the Disk)</span>  
> Command (m for help): **p** <span style="color: #0000ff;">(p=Print partition information)</span>
> 
> Disk /dev/sdh: 107.3 GB, 107374510080 bytes  
> 255 heads, 63 sectors/track, 13054 cylinders  
> Units = cylinders of 16065 * 512 = 8225280 bytes  
> Device Boot      Start         End      Blocks   Id  System  
> **/dev/sdb1               1       13054   104856223+  83  Linux**
> 
> Command (m for help): **t** <span style="color: #0000ff;">(t=type of partition)</span>  
> Selected partition **1** <span style="color: #0000ff;">(Change type of partition 1)</span>  
> Hex code (type L to list codes): **8e** <span style="color: #0000ff;">(8e=LVM partition &#8211; Type L to list partition code)</span>  
> Changed system type of partition 1 to 8e (Linux LVM)  
> Command (m for help): **p** <span style="color: #0000ff;">(</span><span style="color: #0000ff;">p=print partition information)</span>**  
>** 
> 
> Disk /dev/sdh: 107.3 GB, 107374510080 bytes  
> 255 heads, 63 sectors/track, 13054 cylinders  
> Units = cylinders of 16065 * 512 = 8225280 bytes  
> Device Boot      Start         End      Blocks   Id  System  
> **/dev/sdb1               1       13054   104856223+  8e  Linux LVM**
> 
> Command (m for help): **w** <span style="color: #0000ff;">(w=write partition to disk)</span>**  
>** 
> 
> The partition table has been altered!  
> Calling ioctl() to re-read partition table.  
> Syncing disks.

If we look again at our /proc/partition, we should see our new disk and partition are being seen by this server.

> root@gollum~# <span style="color: #0000ff;"><strong>grep sd /proc/partitions</strong></span>  
> 8 0 104857920 sda  
> 8 1 104856223 sda1  
> **<span style="color: #0000ff;">8 16 15728640 sdb</span>**  
>  **<span style="color: #0000ff;">8 16 15727658 sdb1</span>**

Now we need to make sure that the new disk and partition are seen by every servers within the cluster. We now need to go on every servers in the cluster and run the command &#8220;partprobe&#8221; (Partition probe). After running the command, you should check like we did on &#8220;gollum&#8221;, if all the disks and partitions are seen by each servers.

> root@gollum~# <span style="color: #0000ff;"><strong>partprobe</strong></span>  
> root@gandalf~# <span style="color: #0000ff;"><strong>partprobe</strong></span>  
> root@bilbo~# <span style="color: #0000ff;"><strong>partprobe</strong></span>

&nbsp;

**Creating our physical volume**

Now that we know that the disk is seen by every node, let&#8217;s create the physical volume on one of the server and then check on all others servers in the cluster, the physical should be seen on all the servers. The command to create a physical volume is &#8220;pvcreate&#8221;, so what we are really doing here, is creating a physical volume of the partition (/dev/sdb1) we created earlier.

> #** <span style="color: #0000ff;">pvcreate /dev/sdb1</span>**  
> Physical volume &#8220;/dev/sdb1&#8221; successfully created

Let&#8217;s run a pvscan on every node, to validate that every node can actually see the new disk.

> \# **<span style="color: #0000ff;">pvscan</span>  
>** PV /dev/sda1           VG     datavg      lvm2 [100.00 GB / 22.02 GB free]  
> <span style="color: #0000ff;">PV /dev/sdb1                                         lvm2 [100.00 GB]</span>

&nbsp;

**Create our clustered volume group**

We will now create a new volume group named &#8220;sharevg&#8221; and we will assign the physical volume &#8220;/dev/sdb1&#8221; as part of that group. If we ever ran out of disk space within &#8220;sharevg&#8221;, we could add another physical volume to the volume group and continue to work without any service disruption. This is a real advantage when working in production environment.

> **  
> \# <span style="color: #0000ff;">vgcreate  sharevg /dev/sdb1</span>**  
> Clustered volume group &#8220;sharevg&#8221; successfully created

&nbsp;

**Display the &#8220;sharevg&#8221; volume group properties**

We can display the volume group properties by issueing the &#8220;vgdisplay&#8221; command. We can see that our volume group is &#8220;Clustered&#8221;, so it is cluster aware. This will allow later on,  to create &#8220;LV&#8221; (Logical volume/Filesystem) on one server and have the cluster software automatically advise the cluster member that a new logical volume (filesystem) is available.**  
** 

<pre>root@gollum~# <strong><span style="color: #0000ff;">vgdisplay sharevg</span></strong>
 --- Volume group ---
<strong><span style="color: #0000ff;"> VG Name               sharevg</span></strong>
 System ID            
 Format                lvm2
 Metadata Areas        1
 Metadata Sequence No  25
 VG Access             read/write
 VG Status             resizable
<strong><span style="color: #0000ff;"> Clustered             yes</span></strong>
 Shared                no
 MAX LV                0
 Cur LV                0
 Open LV               0
 Max PV                0
 Cur PV                1
 Act PV                1
 VG Size               100.00 GB
 PE Size               4.00 MB
 Total PE              24999
 Alloc PE / Size       1 / 2.10 MB
 Free  PE / Size       24980 / 98.00 GB
 VG UUID               V8Ag76-vdW2-NAk4-JjOo-or3l-GuPz-x5LEKP</pre>

&nbsp;

**Create a logical volume of 1024MB named &#8220;cadmin&#8221; of the &#8220;sharevg&#8221; volume group**

We will create a logical volume named &#8220;cadminlv&#8221; (Cluster Admin), in our sharevg volume group. The command below is asking to create a logical volume of 1024MB, name &#8220;cadminlv&#8221; in the volume group &#8220;sharevg&#8221;.  This command can be done one server and the logical volume will be seen by every member of the cluster.**  
** 

> <span style="color: #0000ff;"><strong>#/usr/sbin/lvcreate -L1024M -n cadminlv sharevg</strong></span>  
> Logical volume &#8220;cadminlv&#8221; created

&nbsp;

The &#8220;lvs&#8221; command allow you to display a list of all your logical volumes. Since this is currently the only one on the volume group &#8220;sharevg&#8221;, we filter the list (with the grep command) to only display the logical volume on &#8220;sharevg&#8221; volume group.Let’s check if it seen by all nodes.

> root@gandalf~# **<span style="color: #0000ff;">lvs | grep sharevg</span>**  
> cadminlv sharevg -wi-a- 1024.00M
> 
> root@gollum~# <span style="color: #0000ff;"><strong>lvs | grep sharevg</strong></span>  
> cadminlv sharevg -wi-a- 1024.00M
> 
> root@bilbo~# <span style="color: #0000ff;"><strong>lvs | grep sharevg</strong></span>  
> cadminlv sharevg -wi-a- 1024.00M

&nbsp;

## Creating the /cadmin GFS Filesystem

Finally, we are going to create a GFS filesystem within that logical volume &#8220;cadminlv&#8221; we&#8217;ve just created. But first we need to create our GFS filesystem mount point on every nodes. We need to do that, because we want this filesystem to be mounted on every node and be available for the 3 nodes.

> root@gandalf~# **<span style="color: #0000ff;">mkdir /cadmin</span>**
> 
> root@bilbo~#    <span style="color: #0000ff;"><strong>mkdir /cadmin</strong></span>
> 
> root@gollum~# <span style="color: #0000ff;"><strong>mkdir /cadmin</strong></span>

&nbsp;

We have choosen to have the GFS filesystem &#8220;/cadmin&#8221; to be mounted on all servers at all time. We could have include it as part of our service, so it would be mounted only when a service is started. But we found out, that the process of unmounting and mounting a couple of GFS take time and this time adds up to the time it take to move a service from one server to another.  In production we have 5 servers in a cluster for more that 2 years now,  we have around 30 GFS mounted at all time on all the five servers and we had very little problem. The only thing you have to be careful about is the number of journals that you assign to each GFS. One journal is needed for each concurrent mount in the cluster, in our case we have at least 5 journals for each of our GFS filesystem (more on that below).Create the GFS on the LVM cadmin created previously. This need to be done only on one node, the creation is done once and all the nodes are made aware of the new GFS by the cluster daemon**.**

The command we use to create a GFS filesystem is &#8220;gfs_mkfs&#8221;.  We need to use a couple of options and I will explain them all.

First, the &#8220;**<span style="color: #0000ff;">-O</span>**&#8221;  prevents  &#8220;gfs_mkfs&#8221;  from  asking  for confirmation before creating the filesystem.

The option &#8220;**<span style="color: #0000ff;">-p lock_dlm</span>**&#8220;, indicate the name of the  locking  protocol to use.  The  locking  protocol should  be  &#8220;lock_dlm&#8221; for a clustered file system.

The &#8220;**<span style="color: #0000ff;">-t our_cluster:cadminlv</span>**&#8221; It&#8217;s the _cluster-name, followed by &#8220;:&#8221; and the logical volume name_.  The cluster name must match the one you have defined in your cluster configuration file (in our case &#8220;our_cluster&#8221;),  only members  of  this cluster are permitted to use this file system.  The filesystem name (cadminlv)  is a unique file system name used to distinguish this GFS file system from others created (1 to 16  characters).

The &#8220;-**<span style="color: #0000ff;">j 4</span>**&#8221; is the number of journals for gfs\_mkfs to create.  You need at least one  journal  per machine that will mount the filesystem. This number should have been 3, but I always add one more, in case a add a member in the cluster. This number is important, if I had put 3 and I added a node within the cluster and I wanted the 4 nodes to mount simultaneously this filesystem, I would need to make sure that the filesystem have 4 journals, because the GFS wound not mount. You can always add a journal to an existing GFS filesystem with the &#8220;gfs\_jadd&#8221; command. **Each journal reserve 128 MB in the filesystem, so you need take into consideration.** Let look at our example, we want all our nodes to mount the &#8220;/cadmin&#8221; GFS filesystem, we created an logical volume of 1024M and on it we created a GFS, we reserved 4 journals (4*128=512MB) **,** so will have only around 500 MB available for data out of the 1024MB we allocated to our logical volume.**  
** 

The last parameter &#8220;**<span style="color: #0000ff;">/dev/sharevg/cadmlv</span>**&#8221; is the name of the logical volume we created previously.

> \# **<span style="color: #0000ff;">/sbin/gfs_mkfs -O -p lock_dlm -t our_cluster:cadmlv -j 4 /dev/sharevg/cadmlv</span>**  
> Device:                    /dev/sharevg/cadmlv  
> Blocksize:                 4096  
> Filesystem Size:           98260  
> Journals:                  4  
> Resource Groups:           8  
> Locking Protocol:          lock_dlm  
> Lock Table:                our_cluster:cadminlv  
> Syncing&#8230;  
> All Done

We are now able to mount our GFS filesystem on all the servers, by using the command below on all the servers,

> **<span style="color: #0000ff;"># mount -t gfs /dev/sharevg/cadminlv  /cadmin</span>**

We want that filesystem to be mounted every time a server boot, so don’t forget to add your filesystem to /etc/fstab, so it will mount after the next reboot and don’t forget to change the owner and protection of the filesystem.

> <span style="color: #0000ff;"><strong># echo “/dev/sharevg/cadminlv /cadmin  gfs defaults 0 0&#8243; >> /etc/fstab</strong></span>

The filesystem should be available on all our nodes.

> **<span style="color: #0000ff;"># df -h /cadmin</span>**  
> Filesystem                                                  Size     Used    Avail    Use%  Mounted on  
> /dev/mapper/sharevg-cadminlv  **<span style="color: #0000ff;">510 M</span>** 1.5M   508M   1%      **<span style="color: #0000ff;">/cadmin</span>**

&nbsp;

So we&#8217;ve just created our first GFS filesystem and it is mounted on all our nodes in the cluster.

<div>
  <p>
    In our last article we will finalise our cluster, by creating the needed scripts for our ftp/web services to start and to move from server to server. We will add these scripts to our cluster configuration and we will show you how to move service from one server to another using the command line and GUI. So stay tune, for this last article on how to build a Red Hat cluster.
  </p>
  
  <p>
    &nbsp;
  </p>
  
  <p>
    <a title="Part 1 - Creating a RedHat/CentOS Cluster" href="http://linternux.com/2010/11/creating-cluster-part-1/" target="_blank" rel="noopener">Part 1 &#8211; Creating a Linux ReadHat/CentOS cluster</a>
  </p>
  
  <p>
    <a href="http://linternux.com/2011/01/creating-a-redhatcentos-cluster-part-2/" target="_blank" rel="noopener">Part 2 &#8211; Creating a Linux ReadHat/CentOS cluster</a>
  </p>
  
  <p>
    <a title="Part 3 of Creating a RedHat/CentOS cluster" href="http://linternux.com/2011/03/creating-a-red-hatcentos-cluster-part-3/" target="_blank" rel="noopener">Part 3 &#8211; Creating a Linux ReadHat/CentOS cluster</a>
  </p>
  
  <p>
    <a title="Part 4 of Creating a RedHat/CentOS cluster" href="http://linternux.com/2011/04/creating-a-redhat-cluster-part-4/" target="_blank" rel="noopener">Part 4 &#8211; Creating a Linux ReadHat/CentOS cluster</a>
  </p>
  
  <p>
    <a title="Part 5 of Creating a RedHat/CentOS cluster" href="http://linternux.com/2011/04/creating-a-redhat-cluster-part-5/" target="_blank" rel="noopener">Part 5 &#8211; Creating a Linux ReadHat/CentOS cluster</a>
  </p>
  
  <p>
    &nbsp;
  </p>
</div>