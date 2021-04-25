---
title: Doing more with less
date: 2021-03-29 10:03:03
layout: post
categories:
  - General 
tags:
  - less
  - more
  - unzip
  - zip
---

We usually use the &#8220;less&#8221; command to browse the content of a text file. We use the 
[Page-Up], [Page-Down], [Down-Arrow], [Up-Arrow] keys to move within the file, but did you know 
that you can do more with the less command ?

<!--more-->

<img src="/assets/img/sadm_more_less.jpg" class="align-center" alt="">

<center>
Image by <a href="https://pixabay.com/users/kaboompics-1013994/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=791109">Karolina Grabowska</a> from <a href="https://pixabay.com/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=791109">Pixabay</a>
</center><br>

## You can see the content of a rpm file

### # less ./unzip-6.0-21.el7.x86_64.rpm
<pre>
Name        : unzip
Version     : 6.0
Release     : 21.el7
Architecture: x86_64
Install Date: (not installed)
Group       : Applications/Archiving
Size        : 374042
License     : BSD
Signature   : RSA/SHA256, Fri 03 Apr 2020 05:09:58 PM EDT, Key ID 24c6a8a7f4a80eb5
Source RPM  : unzip-6.0-21.el7.src.rpm
Build Date  : Wed 01 Apr 2020 12:49:28 AM EDT
Build Host  : x86-02.bsys.centos.org
Relocations : (not relocatable)
Packager    : CentOS BuildSystem <http://bugs.centos.org>
Vendor      : CentOS
URL         : http://www.info-zip.org/UnZip.html
Summary     : A utility for unpacking zip files
Description :
The unzip utility is used to list, test, or extract files from a zip
archive.  Zip archives are commonly found on MS-DOS systems.  The zip
utility, included in the zip package, creates zip archives.  Zip and
unzip are both compatible with archives created by PKWARE(R)'s PKZIP
for MS-DOS, but the programs' options and default behaviors do differ
in some respects.

Install the unzip package if you need to list, test or extract files from
a zip archive.
* Mon Nov 04 2019 Jakub Martisko <jamartis@redhat.com> - 6.0-21
- Fix CVE-2019-13232
- Resolves: CVE-2019-13232

-rwxr-xr-x    1 root    root      32136 Apr  1  2020 /usr/bin/funzip
-rwxr-xr-x    2 root    root     185512 Apr  1  2020 /usr/bin/unzip
-rwxr-xr-x    1 root    root      90760 Apr  1  2020 /usr/bin/unzipsfx
-rwxr-xr-x    1 root    root       2953 Oct 10  2008 /usr/bin/zipgrep
-rwxr-xr-x    2 root    root     185512 Apr  1  2020 /usr/bin/zipinfo
drwxr-xr-x    2 root    root          0 Apr  1  2020 /usr/share/doc/unzip-6.0
-rw-r--r--    1 root    root       4633 Jan  2  2009 /usr/share/doc/unzip-6.0/BUGS
-rw-r--r--    1 root    root       3606 Jan  3  2009 /usr/share/doc/unzip-6.0/LICENSE
-rw-r--r--    1 root    root      18337 Apr 19  2009 /usr/share/doc/unzip-6.0/README
-rw-r--r--    1 root    root       1984 Apr 19  2009 /usr/share/man/man1/funzip.1.gz
-rw-r--r--    1 root    root      18132 Apr  1  2020 /usr/share/man/man1/unzip.1.gz
-rw-r--r--    1 root    root       5512 Apr 19  2009 /usr/share/man/man1/unzipsfx.1.gz
-rw-r--r--    1 root    root       1543 Apr 19  2009 /usr/share/man/man1/zipgrep.1.gz
-rw-r--r--    1 root    root       8934 Apr  1  2020 /usr/share/man/man1/zipinfo.1.gz
</pre>

<br>

## View the content of zip file

### $ less StudioPress.zip
<pre>
Archive:  StudioPress.zip   196759 bytes   42 files
drwxr-xr-x  2.3 unx        0 bx stor 17-Sep-08 09:05 StudioPress/
-rw-r--r--  2.3 unx     3848 tx defN 14-Jan-08 09:05 StudioPress/comments.php
-rw-r--r--  2.3 unx      231 tx defN 14-Jan-08 09:05 StudioPress/searchform.php
-rw-r--r--  2.3 unx      169 tx defN  4-Feb-08 09:44 StudioPress/adsense_top.php
-rw-r--r--  2.3 unx     1393 tx defN  5-Feb-08 06:50 StudioPress/home.php
-rw-r--r--  2.3 unx     1411 tx defN 14-Jan-08 09:05 StudioPress/functions.php
</pre>



<br>

## **View the content of tar or tgz file** 

### $ less update_inv.tar
<pre>
drwxr-xr-x jacques/jacques   0 2008-01-03 20:48:50 update_inv/
drwxr-xr-x jacques/jacques   0 2008-03-22 13:52:07 update_inv/log/
-rw-rw-r-- jacques/jacques   0 2008-03-22 13:52:07 update_inv/log/update_inv.log
drwxr-xr-x jacques/jacques   0 2008-03-22 13:52:33 update_inv/html/
-rw-rw-r-- jacques/jacques 941 2008-03-26 19:24:51 update_inv/html/gumby.html
drwxr-xr-x jacques/jacques   0 2008-03-22 13:52:07 update_inv/tmp/
-rwxrwxr-x jacques/jacques  19059 2008-03-26 19:00:43 update_inv/update_inventory.py
update_inv.tar 
</pre>


