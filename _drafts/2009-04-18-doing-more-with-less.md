---
id: 30
title: Doing more with less
date: 2009-04-18T09:41:12+00:00
author: jacques
excerpt: 'We used the "less" command to browse the content of a text file. We use the [PageUp], [PageDown], [DownArrow], [UpArrow] keys to move within the file, but did you know that you can do more with the less command ?'
layout: post
guid: http://blog.linternux.com/?p=30
permalink: /index.php/2009/04/18/doing-more-with-less/
categories:
  - Scripting
tags:
  - less
---
We usually use the &#8220;less&#8221; command to browse the content of a text file. We use the [Page-Up], [Page-Down], [Down-Arrow], [Up-Arrow] keys to move within the file, but did you know that you can do more with the less command ?

#### You can see the content of a rpm file

<pre><strong># less flash-plugin-9.0.124.0-release.i386.rpm</strong>
 Name        : flash-plugin                 Relocations: (not relocatable)
 Version     : 9.0.124.0                    Vendor: Adobe Systems Inc.
 Release     : release                      Build Date: Tue 25 Mar 2008 12:21:32 PM EDT
 Install Date: (not installed)              Build Host: fplayerbuild2-lnx.macromedia.com
 Group       : Applications/Internet        Source RPM: flash-plugin-9.0.124.0-release.src.rpm
 Size        : 8161247                      License: Commercial
 Signature   : (none)
 Packager    : Adobe Systems Inc.
 URL         : http://www.adobe.com/downloads/
 Summary     : Adobe Flash Player 7.0</pre>

<pre>drwxr-xr-x     2 root    root        0 Mar 25  2008 /usr/lib/flash-plugin
 -rw-r--r--    1 root    root    34516 Mar 24  2008 /usr/lib/flash-plugin/LICENSE
 -rw-r--r--    1 root    root     3180 Mar 24  2008 /usr/lib/flash-plugin/README
 -rwxr--r--    1 root    root     1367 Mar 24  2008 /usr/lib/flash-plugin/homecleanup
 -rwxr-xr-x    1 root    root   115888 Mar 25  2008 /usr/lib/flash-plugin/libflashplayer.so
 -rwxr--r--    1 root    root     3116 Mar 24  2008 /usr/lib/flash-plugin/setup
 drwxr-xr-x    2 root    root        0 Mar 25  2008 /usr/share/doc/flash-plugin-9.0.124.0
 -rw-r--r--    1 root    root     3180 Mar 25  2008 /usr/share/doc/flash-plugin-9.0.124.0/readme.txt
 (END)</pre>

#### View the content of zip file :

<pre><strong>$ less StudioPress.zip</strong>
Archive:  StudioPress.zip   196759 bytes   42 files
drwxr-xr-x  2.3 unx        0 bx stor 17-Sep-08 09:05 StudioPress/
-rw-r--r--  2.3 unx     3848 tx defN 14-Jan-08 09:05 StudioPress/comments.php
-rw-r--r--  2.3 unx      231 tx defN 14-Jan-08 09:05 StudioPress/searchform.php
-rw-r--r--  2.3 unx      169 tx defN  4-Feb-08 09:44 StudioPress/adsense_top.php
-rw-r--r--  2.3 unx     1393 tx defN  5-Feb-08 06:50 StudioPress/home.php
-rw-r--r--  2.3 unx     1411 tx defN 14-Jan-08 09:05 StudioPress/functions.php</pre>

#### **View the content of tar or tgz file** 

<pre><strong>$ less update_inv.tar</strong>
drwxr-xr-x jacques/jacques   0 2008-01-03 20:48:50 update_inv/
drwxr-xr-x jacques/jacques   0 2008-03-22 13:52:07 update_inv/log/
-rw-rw-r-- jacques/jacques   0 2008-03-22 13:52:07 update_inv/log/update_inv.log
drwxr-xr-x jacques/jacques   0 2008-03-22 13:52:33 update_inv/html/
-rw-rw-r-- jacques/jacques 941 2008-03-26 19:24:51 update_inv/html/gumby.html
drwxr-xr-x jacques/jacques   0 2008-03-22 13:52:07 update_inv/tmp/
-rwxrwxr-x jacques/jacques  19059 2008-03-26 19:00:43 update_inv/update_inventory.py
update_inv.tar (END)</pre>