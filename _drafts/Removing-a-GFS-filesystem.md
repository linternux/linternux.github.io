---
id: 1676
title: Removing a GFS filesystem
date: 2011-03-19T14:16:40+00:00
author: jacques
layout: post
guid: http://linternux.com/?p=1676
permalink: /?p=1676
aktt_notify_twitter:
  - 'yes'
categories:
  - General
tags:
  - cluster
  - gfs
---
We need to unmount the filesystem on EVERY node that use it. If the logical volume id still in use we will not be able to remove it.

&nbsp;

<div>
  <p>
    root@lxmq1027~# <strong>lvremove /dev/cluster_vg/cadminlv</strong>
  </p>
  
  <p>
    Do you really want to remove active logical volume &#8220;cadminlv&#8221;? [y/n]: y
  </p>
  
  <p>
    <strong> Error locking on node lxmq1027.slac.ca: Volume is busy on another node</strong>
  </p>
  
  <p>
    Can&#8217;t get exclusive access to volume &#8220;cadminlv&#8221;
  </p>
  
  <p>
    &nbsp;
  </p>
  
  <p>
    Then we deactivate the logical volume, but putting the activate flag to ‘n’.
  </p>
  
  <div>
    <p>
      root@lxmq1027~# <strong>lvchange -an  /dev/cluster_vg/cadminlv</strong>
    </p>
    
    <p>
      &nbsp;
    </p>
    
    <p>
      Then we can remove the logical volume
    </p>
    
    <div>
      <p>
        root@lxmq1027~# <strong>lvremove /dev/cluster_vg/cadminlv</strong>
      </p>
      
      <p>
        Logical volume &#8220;cadminlv&#8221; successfully removed
      </p>
      
      <p>
        root@lxmq1027~#
      </p>
      
      <p>
        &nbsp;
      </p>
      
      <p>
        Unmount filesystem on the cluster<br /> lxmq1018.slac.ca..lxmq1024.slac.ca..lxmq1026.slac.ca..lxmq1019.slac.ca..<br /> Running : /usr/sbin/lvchange -an /dev/sharevg/admlv2<br /> Running : lvremove -f /dev/sharevg/admlv2
      </p>
      
      <p>
        Running : Removing mount point in /etc/fstab on all cluster nodes<br /> lxmq1018.slac.ca..lxmq1024.slac.ca..lxmq1026.slac.ca..lxmq1019.slac.ca..<br /> Running : rmdir /cadm on the cluster nodes<br /> lxmq1018.slac.ca..lxmq1024.slac.ca..lxmq1026.slac.ca..lxmq1019.slac.ca..
      </p>
    </div>
  </div>
</div>