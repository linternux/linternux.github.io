---
id: 79
title: Adding SAN disk without rebooting
date: 2009-06-23T14:00:15+00:00
author: jacques
layout: post
guid: http://blog.linternux.com/?p=79
permalink: /index.php/2009/06/23/adding-san-disk/
categories:
  - Storage
tags:
  - disk
  - san
---
The production system is running, the database filesystem is almost full. We need more disk space on the server. The server is attached to the San so we can add disk without bringing down the server. In this article, we will demonstrate what is needed to make the new San disk available to the server without disturbing the running application. Although that theses commands have been tested and used on production environment,  I suggest that you build your confidence first, by trying them on a development system.

**<span style="color: #0000ff;">On Red Hat / Centos 5</span>**

In our example, we have presently one disk accessible via one fiber HBA on our server. We can use to &#8220;lsscsi&#8221; command to list our scsi device. If you do not have the command &#8216;lsscsi&#8217; command installed, you can install it by issuing the following command &#8220;yum install lsscsi&#8221;.

Our server can see one disk at the moment ;

<pre>#  lsscsi
[0:0:0:0]    disk    HITACHI  OPEN-V           5009   /dev/sda</pre>

Let rescan our SCSI bus. Since we have only one fiber card in our server, we will scan only the first HBA (host0). If you have two fiber card, you may need to issue the &#8220;partprobe&#8221; command for the disk to be seen on the second interface.

<pre># echo "1" &gt; /sys/class/fc_host/host0/issue_lip</pre>

We now see the new SAN disk that was newly allocated to the system.

<pre># lsscsi
[0:0:0:0]    disk    HITACHI  OPEN-V           5009   /dev/sda
[0:0:0:1]    disk    HITACHI  OPEN-V           5009   /dev/sdb</pre>

For other kind of SCSI attached storage, we should rescan the bus with this command :

<pre># echo "- - -" &gt; /sys/class/scsi_host/host0/scan   (First hba)
# echo "- - -" &gt; /sys/class/scsi_host/host1/scan   (Second hba, if present)</pre>

**<span style="color: #0000ff;"><!--more-->On Red Hat / Centos 3 and 4</span>**

The current situation is that we have one disk (lun 2), that is seen by the two fiber cards on our server.

<pre># cd /proc/scsi/sg
# cat device_hdr devices
host    chan    id      lun     type    opens   qdepth  busy    online
0       0       0       2       0       1       64      0       1
1       0       0       2       0       1       64      0       1
#</pre>

These commands trigger the driver to initiate lun discovery process

<pre># echo "scsi-qlascan" &gt; /proc/scsi/qla2300/0    # For Qlogic Fiber card 0
# echo "scsi-qlascan" &gt; /proc/scsi/qla2300/1    # For Qlogic Fiber card 1</pre>

Lets force the scsi mid layer to do its own scan and build the device table entry for the new device:  
We are adding the LUN 04

<pre># echo "scsi add-single-device 00 00 00 04" &gt; /proc/scsi/scsi
# echo "scsi add-single-device 01 00 00 04" &gt; /proc/scsi/scsi</pre>

Let see if the new LUN was Assigned (Visible) by Linux

<pre># cd /proc/scsi/sg
# cat device_hdr devices
host    chan    id      lun     type    opens   qdepth  busy    online
0       0       0       2       0       1       64      0       1
<strong>0       0       0       4       0       0       64      0       1</strong>
1       0       0       2       0       1       64      0       1
<strong>1       0       0       4       0       0       64      0       1</strong>
#</pre>

In the command &#8220;echo &#8220;scsi add-single-device HH CC TT LL &#8221; >/proc/scsi/scsi&#8221;

**The path name should have the following format HHCCTTLL :  
HH=Adapter number (HBA#)    CC=Channel ID    TT=SCSI ID   LL=LUN ID OF DEVICE**

 ****<figure id="attachment_512" aria-describedby="caption-attachment-512" style="width: 300px" class="wp-caption alignleft">

****<img loading="lazy" class="size-medium wp-image-512" title="scli" src="http://192.168.1.88/wp-content/uploads/2009/06/scli-300x290.png" alt="Qlogic Command Line Interface" width="300" height="290" />****<figcaption id="caption-attachment-512" class="wp-caption-text">Qlogic Command Line Interface</figcaption></figure> 

Qlogic have a command line tool called &#8220;scli&#8221; that really simplify the operation of adding SAN disk on Redhat/Centos/Fedora. It can also be used to obtain Fiber card information and setting.

![](file:///C:/DOCUME~1/JADUPL~1.SLA/LOCALS~1/Temp/moz-screenshot.jpg) 

Download it for [Redhat 32 Bits](http://driverdownloads.qlogic.com/QLogicDriverDownloads_UI/SearchByOs.aspx?ProductCategory=39&OsCategory=2&Os=26&OsCategoryName=Linux&ProductCategoryName=Fibre%20Channel%20HBAs&OSName=Linux%20Red%20Hat%20(32-bit) "Qlogic RedHat 32 Bits Download Page") or [RedHat 64Bits](http://driverdownloads.qlogic.com/QLogicDriverDownloads_UI/SearchByOs.aspx?ProductCategory=39&OsCategory=2&Os=65&OsCategoryName=Linux&ProductCategoryName=Fibre%20Channel%20HBAs&OSName=Linux%20Red%20Hat%20(64-bit) "Qlogic RedHat 64 Bits Download Page").  
Hope to see you soon !