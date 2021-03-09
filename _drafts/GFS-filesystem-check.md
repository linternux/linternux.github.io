---
id: 1671
title: GFS filesystem check
date: 2011-03-15T20:17:36+00:00
author: jacques
layout: post
guid: http://linternux.com/?p=1671
permalink: /?p=1671
aktt_notify_twitter:
  - 'yes'
categories:
  - General
tags:
  - cluster
  - gfs
---
When nodes fail with the file system mounted, file-system journaling allows fast recovery.  However, if a storage device loses power or is physically disconnected,  file-system corruption may occur. (Journaling cannot be used to recover from

storage subsystem failures.) When that type of corruption occurs, you can recover the GFS file system by  using the gfs_fsck command.

&nbsp;

The gfs_fsck command must only be run on a file system that is **unmounted from all nodes**

<div>
  <p>
    root@lxmq1028~# <strong>umount /cltest</strong>
  </p>
  
  <p>
    root@lxmq1029~# <strong>umount /cltest</strong>
  </p>
  
  <p>
    root@lxmq1033~# <strong>umount /cltest </strong>
  </p>
  
  <p>
    root@lxmq1027~# <strong>umount /cltest</strong>
  </p>
  
  <p>
    &nbsp;
  </p>
  
  <p>
    root@lxmq1033~# <strong>gfs_fsck -y /dev/cluster_vg/clutest</strong>
  </p>
  
  <p>
    Initializing fsck
  </p>
  
  <p>
    Clearing journals (this may take a while)&#8230;
  </p>
  
  <p>
    Journals cleared.
  </p>
  
  <p>
    Starting pass1
  </p>
  
  <p>
    Pass1 complete
  </p>
  
  <p>
    Starting pass1b
  </p>
  
  <p>
    Pass1b complete
  </p>
  
  <p>
    Starting pass1c
  </p>
  
  <p>
    Pass1c complete
  </p>
  
  <p>
    Starting pass2
  </p>
  
  <p>
    Pass2 complete
  </p>
  
  <p>
    Starting pass3
  </p>
  
  <p>
    Pass3 complete
  </p>
  
  <p>
    Starting pass4
  </p>
  
  <p>
    Pass4 complete
  </p>
  
  <p>
    Starting pass5
  </p>
  
  <p>
    Pass5 complete
  </p>
  
  <p>
    Writing changes to disk
  </p>
  
  <p>
    root@lxmq1033~#
  </p>
  
  <p>
    &nbsp;
  </p>
  
  <p>
    &nbsp;
  </p>
</div>