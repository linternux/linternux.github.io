---
id: 1669
title: Increasing a GFS filesystem
date: 2011-03-15T20:16:24+00:00
author: jacques
layout: post
guid: http://linternux.com/?p=1669
permalink: /?p=1669
aktt_notify_twitter:
  - 'yes'
categories:
  - General
tags:
  - cluster
  - gfs
---
In this example we have a logical volume of 2GB contains a GFS filesystem with 4 GFS journal

(Logical Volume of 2GB – (4Journals*128MB) = 1.5GB usable space). We will increase the filesystem by 1GB. The operation can be performed on any node on witch the filesystem is mounted within the cluster. The operation will performed automatically on the other nodes.

&nbsp;

<div>
  <p>
    root@lxmq1033~# <strong>lvs | grep newlv</strong>
  </p>
  
  <p>
    newlv       newvg      -wi-ao   <strong>2.00G</strong>
  </p>
  
  <p>
    &nbsp;
  </p>
  
  <p>
    root@lxmq1033~# <strong>df -h /newlv</strong>
  </p>
  
  <p>
    Filesystem            Size  Used Avail Use% Mounted on
  </p>
  
  <p>
    /dev/newvg/newlv      <strong>1.5G</strong> 20K  1.5G   1% /newlv
  </p>
  
  <p>
    &nbsp;
  </p>
  
  <p>
    &nbsp;
  </p>
  
  <p>
    root@lxmq1033~# <strong>lvextend -L+1G /dev/newvg/newlv </strong>
  </p>
  
  <p>
    Extending logical volume newlv to 3.00 GB
  </p>
  
  <p>
    Logical volume newlv successfully resized
  </p>
  
  <p>
    &nbsp;
  </p>
  
  <p>
    root@lxmq1033~# <strong>lvs | grep newlv</strong>
  </p>
  
  <p>
    newlv       newvg      -wi-ao   3.00G
  </p>
  
  <p>
    &nbsp;
  </p>
  
  <p>
    &nbsp;
  </p>
  
  <p>
    root@lxmq1033~# <strong>gfs_grow /newlv</strong>
  </p>
  
  <p>
    FS: Mount Point: /newlv
  </p>
  
  <p>
    FS: Device: /dev/newvg/newlv
  </p>
  
  <p>
    FS: Options: rw,hostdata=jid=0:id=262148:first=0
  </p>
  
  <p>
    FS: Size: 524288
  </p>
  
  <p>
    DEV: Size: 786432
  </p>
  
  <p>
    Preparing to write new FS information&#8230;
  </p>
  
  <p>
    Done.
  </p>
  
  <p>
    &nbsp;
  </p>
  
  <p>
    root@lxmq1033~# <strong>df -h /newlv</strong>
  </p>
  
  <p>
    Filesystem            Size  Used Avail Use% Mounted on
  </p>
  
  <p>
    /dev/newvg/newlv      <strong>2.5G</strong> 20K  2.5G   1% /newlv
  </p>
  
  <p>
    root@lxmq1033~#
  </p>
  
  <p>
    &nbsp;
  </p>
  
  <p>
    Verify on the other nodes if the filesystem increase is reflected.
  </p>
  
  <p>
    &nbsp;
  </p>
  
  <p>
    &nbsp;
  </p>
  
  <p>
    &nbsp;
  </p>
</div>