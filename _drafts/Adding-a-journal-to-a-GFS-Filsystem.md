---
id: 1674
title: Adding a journal to a GFS Filsystem
date: 2011-03-15T20:19:20+00:00
author: jacques
layout: post
guid: http://linternux.com/?p=1674
permalink: /?p=1674
aktt_notify_twitter:
  - 'yes'
categories:
  - General
tags:
  - cluster
  - gfs
---
The gfs\_jadd command is used to add journals to a GFS file system after the device where the file system resides has been expanded.  Running a gfs\_jadd command on a GFS file system uses space between the current end of the file system and the end of the device where the file system resides. When the fill operation is completed, the journal index is updated.

&nbsp;

The gfs_jadd command must be run on mounted file system, but it only needs to be run on one node in the cluster. All the other nodes sense that the expansion has occurred.

&nbsp;

To verify that the changes were successful, use the gfs_jadd command with the -T (test) and -v (verbose) flags. Running the command with those flags displays the current state of the mounted GFS file system

<div>
  <p>
    root@lxmq1033:~# <strong>gfs_tool df /WebSphere/dmgr</strong>
  </p>
  
  <p>
    &nbsp;
  </p>
  
  <p>
    <strong>/WebSphere/dmgr:</strong>
  </p>
  
  <p>
    SB lock proto = &#8220;lock_dlm&#8221;
  </p>
  
  <p>
    SB lock table = &#8220;wasprod:ldmgrlv&#8221;
  </p>
  
  <p>
    SB ondisk format = 1309
  </p>
  
  <p>
    SB multihost format = 1401
  </p>
  
  <p>
    Block size = 4096
  </p>
  
  <p>
    <strong>Journals = 2</strong>
  </p>
  
  <p>
    Resource Groups = 16
  </p>
  
  <p>
    Mounted lock proto = &#8220;lock_dlm&#8221;
  </p>
  
  <p>
    Mounted lock table = &#8220;wasprod:ldmgrlv&#8221;
  </p>
  
  <p>
    Mounted host data = &#8220;jid=0:id=1245187:first=1&#8221;
  </p>
  
  <p>
    Journal number = 0
  </p>
  
  <p>
    Lock module flags = 0
  </p>
  
  <p>
    Local flocks = FALSE
  </p>
  
  <p>
    Local caching = FALSE
  </p>
  
  <p>
    Oopses OK = FALSE
  </p>
  
  <p>
    &nbsp;
  </p>
  
  <p>
    Type           Total          Used           Free           use%
  </p>
  
  <p>
    &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;
  </p>
  
  <p>
    inodes         23393          23393          0              100%
  </p>
  
  <p>
    metadata       7652           652            7000           9%
  </p>
  
  <p>
    data           1004131        354574         649557         35%
  </p>
  
  <p>
    root@lxmq1033:~#
  </p>
  
  <p>
    root@lxmq1033:~#
  </p>
  
  <p>
    root@lxmq1033:~# <strong>df -h /WebSphere/dmgr</strong>
  </p>
  
  <p>
    Filesystem            Size  Used Avail Use% Mounted on
  </p>
  
  <p>
    /dev/mapper/lx1033a_vg-dmgrlv
  </p>
  
  <p>
    <strong>4.0G</strong> 1.5G  2.6G  37% /WebSphere/dmgr
  </p>
  
  <p>
    &nbsp;
  </p>
  
  <h3>
    1.1.1       Adding space for additional journals
  </h3>
  
  <p>
    &nbsp;
  </p>
  
  <p>
    For each additional journal we need 256 MB, to be added to the logical volume. Even if there is available space within the file system, the logical volume is fully allocated to the file system. So we MUST add space to the logical volume in order to add GFS journal to it.
  </p>
  
  <div>
    <p>
      root@lxmq1033:~# <strong>gfs_jadd -j 3  /WebSphere/dmgr  (3=Number of Journal to add)</strong>
    </p>
    
    <p>
      Requested size (98304 blocks) greater than available space (0 blocks)
    </p>
    
    <p>
      &nbsp;
    </p>
    
    <p>
      root@lxmq1033:~# <strong>lvextend -L+512MB /dev/lx1033a_vg/dmgrlv</strong>
    </p>
    
    <p>
      Extending logical volume dmgrlv to 4.30 GB
    </p>
    
    <p>
      Logical volume dmgrlv successfully resized
    </p>
    
    <p>
      &nbsp;
    </p>
    
    <p>
      root@lxmq1033:~# <strong>gfs_jadd -j 3  /WebSphere/dmgr</strong>
    </p>
    
    <p>
      FS: Mount Point: /WebSphere/dmgr
    </p>
    
    <p>
      FS: Device: /dev/mapper/lx1033a_vg-dmgrlv
    </p>
    
    <p>
      FS: Options: rw,hostdata=jid=0:id=1245187:first=1
    </p>
    
    <p>
      FS: Size: 1100800
    </p>
    
    <p>
      DEV: Size: 1203200
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
      root@lxmq1033:~# <strong>lvscan | grep dmgr</strong>
    </p>
    
    <p>
      ACTIVE            &#8216;/dev/lx1033a_vg/dmgrlv&#8217; [4.59 GB] inherit
    </p>
    
    <p>
      &nbsp;
    </p>
    
    <p>
      &nbsp;
    </p>
    
    <p>
      root@lxmq1033:~# <strong>gfs_tool df /WebSphere/dmgr</strong>
    </p>
    
    <p>
      /WebSphere/dmgr:
    </p>
    
    <p>
      SB lock proto = &#8220;lock_dlm&#8221;
    </p>
    
    <p>
      SB lock table = &#8220;wasprod:ldmgrlv&#8221;
    </p>
    
    <p>
      SB ondisk format = 1309
    </p>
    
    <p>
      SB multihost format = 1401
    </p>
    
    <p>
      Block size = 4096
    </p>
    
    <p>
      <strong>Journals = 5</strong>
    </p>
    
    <p>
      Resource Groups = 16
    </p>
    
    <p>
      Mounted lock proto = &#8220;lock_dlm&#8221;
    </p>
    
    <p>
      Mounted lock table = &#8220;wasprod:ldmgrlv&#8221;
    </p>
    
    <p>
      Mounted host data = &#8220;jid=0:id=1245187:first=1&#8221;
    </p>
    
    <p>
      Journal number = 0
    </p>
    
    <p>
      Lock module flags = 0
    </p>
    
    <p>
      Local flocks = FALSE
    </p>
    
    <p>
      Local caching = FALSE
    </p>
    
    <p>
      Oopses OK = FALSE
    </p>
    
    <p>
      &nbsp;
    </p>
    
    <p>
      Type           Total          Used           Free           use%
    </p>
    
    <p>
      &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;
    </p>
    
    <p>
      inodes         23393          23393          0              100%
    </p>
    
    <p>
      metadata       7652           652            7000           9%
    </p>
    
    <p>
      data           1004131        354574         649557         35%
    </p>
    
    <p>
      <a href="mailto:root@lxmq1033:%7E#">root@lxmq1033:~#</a>
    </p>
    
    <p>
      &nbsp;
    </p>
  </div>
  
  <p>
    &nbsp;
  </p>
</div>

&nbsp;