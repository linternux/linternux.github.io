---
id: 196
title: Stripping Logical volume accross physical volume
date: 2009-07-02T20:01:24+00:00
author: jacques
layout: post
guid: http://linternux.com/?p=196
permalink: /?p=196
categories:
  - Storage
tags:
  - disk
  - lvm
  - Storage
---
Ever

The following command creates a striped logical volume across 2 physical volumes with a stride of 64kB. The logical volume is 50 gigabytes in size, is named <tt class="literal">gfslv</tt>, and is carved out of volume group <tt class="literal">vg0</tt>.

<pre class="screen">lvcreate -L 50G -i2 -I64 -n gfslv vg0</pre>

As with linear volumes, you can specify the extents of the physical volume that you are using for the stripe. The following command creates a striped volume 100 extents in size that stripes across two physical volumes, is named <tt class="literal">stripelv</tt> and is in volume group <tt class="literal">testvg</tt>. The stripe will use sectors 0-50 of <tt class="literal">/dev/sda1</tt> and sectors 50-100 of <tt class="literal">/dev/sdb1</tt>.

<pre class="screen"># <strong class="userinput"><tt>lvcreate -l 100 -i2 -nstripelv testvg /dev/sda1:0-50 /dev/sdb1:50-100</tt></strong>
  Using default stripesize 64.00 KB
  Logical volume "stripelv" created</pre>

<pre># lvdisplay -m /dev/datavg/devlv
  --- Logical volume ---
  LV Name                /dev/datavg/devlv
  VG Name                datavg
  LV UUID                X5icET-DKLg-DOfV-udaV-vgY3-E03c-Iy0z5s
  LV Write Access        read/write
  LV Status              available
  # open                 1
  LV Size                11.72 GB
  Current LE             3001
  Segments               2
  Allocation             inherit
  Read ahead sectors     0
  Block device           253:33

  --- Segments ---
  Logical extent 0 to 1215:
    Type                linear
    Physical volume     /dev/sde1
    Physical extents    11901 to 13116

  Logical extent 1216 to 3000:
    Type                linear
    Physical volume     /dev/sde1
    Physical extents    13484 to 15268
#</pre>