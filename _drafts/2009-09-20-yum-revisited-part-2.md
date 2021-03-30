---
id: 780
title: Yum Revisited (Part 2)
date: 2009-09-20T07:40:11+00:00
author: jacques
layout: post
guid: http://linternux.com/?p=780
permalink: /index.php/2009/09/20/yum-revisited-part-2/
categories:
  - Admin
tags:
  - Admin
  - rpm
  - yum
---
[<img loading="lazy" class="alignleft size-full wp-image-779" title="YUM - Yellowdog Updater Modified" src="http://192.168.1.88/wp-content/uploads/2009/09/timthumb.png" alt="YUM - Yellowdog Updater Modified" width="96" height="96" />](http://192.168.1.88/wp-content/uploads/2009/09/timthumb.png) Here is the second article dedicated to the &#8220;yum&#8221; command. We will demonstrate some useful option of the command and how-to recover in some problematic situation. Keeping your system up to date is important, knowing how to do it and how to correct some issue related to rpm are also important. So let&#8217;s see if everyone can find something new about the &#8220;yum&#8221; command and see if we can apply it in our daily system admin job. You can also consult the [&#8220;yum&#8221; website](http://yum.baseurl.org/ "YUM Website") or the [&#8220;yum&#8221; wiki](http://en.wikipedia.org/wiki/Yellowdog_Updater,_Modified "YUM Wiki") to obtain more information. As always, I will try to make this article as useful as it can be, so we  can all come back to it and use it as a reference to the &#8220;yum&#8221; command.

### **Checking if update are available**

Let&#8217;s say you want to be advise when new update are available and want to be emailed with the list of packages included in the update.  When we run the &#8220;yum&#8221; command with the &#8220;check-update&#8221; parameter, it actually go and check if any update are available at the repository you have defined in your &#8220;/etc/yum.repos.d&#8221; directory. If any update are available, they will be listed and the program exit code returned will be 100. If no update are available then the exit code will be 0.

<pre>#<strong> <span style="color: #0000ff;">yum check-update ; echo $?</span></strong>
Loaded plugins: changelog, fastestmirror, priorities
Loading mirror speeds from cached hostfile
* base: mirrors.portafixe.com
* updates: mirrors.portafixe.com
* addons: mirrors.portafixe.com
* extras: mirrors.portafixe.com
cman.i386                                2.0.98-1.el5_3.7       updates
ksh.i386                                 20080202-2.el5_3.1     updates
openais.i386                             0.80.3-22.el5_3.9      updates
rgmanager.i386                           2.0.46-1.el5.centos.4  updates
tzdata.noarch                            2009k-1.el5            updates
<span style="color: #0000ff;"><strong>100</strong></span>
#</pre>

We can easily make a little script that we can put in our system crontab file, that will check if any update are available. If there are any, then a list of the updated package will be send to you by email

<pre>#!/bin/sh
/usr/bin/yum check-update &gt; /tmp/cu$$ 2&gt;&1
RET=$?
if [ $RET -eq 100 ]; then
   cat /tmp/cu$$ | mail -s "System Update Available" yourname@gmail.com
fi
rm /tmp/cu$$ &gt; /dev/null 2&gt;&1</pre>

<!--more-->

### **Updating your system**

To apply all the update available for your system, use the &#8220;yum update&#8221; command. Updated packages will be downloaded and then a confirmation will be ask before applying them. If you wish to download and install all the update without confirmation use &#8220;-y&#8221; parameter to skip the confirmation.

<pre># <span style="color: #0000ff;"><strong>yum -y update</strong></span>
Loaded plugins: downloadonly, fastestmirror
Loading mirror speeds from cached hostfile
 * extras: mirrors.portafixe.com
 * base: mirrors.portafixe.com
 * updates: mirrors.portafixe.com
 * addons: mirrors.portafixe.com
Setting up Update Process
No Packages marked for Update
#</pre>

### **Removing a package**

To remove a package, simply specify the name of the package to be remove, using the &#8220;yum erase&#8221; command.

<pre># <strong><span style="color: #0000ff;">yum erase bind</span></strong></pre>

### **What RPM provide this file ?**

Let&#8217;s say, you need a file, a library or a program and you are wondering what package is providing it. Well, there are two ways you can find what rpm include the file you are searching. If the file is already installed on your server and you want to know what package is supplying it, you can use the &#8221; rpm&#8221; command to find it. But you have to specify the full path of the program in order to work. As an example, let&#8217;s say you want to know what package is supplying the &#8220;awk&#8221; program and that it is already installed on your server, you can use the instruction below.

<pre># <span style="color: #000000;"><strong><span style="color: #0000ff;">rpm -qf awk</span>   (Need to specify full path & must exist on your server)</strong>
</span>error: file /awk: No such file or directory

# <strong>Must be on server & need to specify full path in order to find the rpm
</strong># <strong><span style="color: #0000ff;">type awk</span></strong>
awk is /usr/bin/awk
# <strong><span style="color: #0000ff;">rpm -qf /usr/bin/awk</span></strong>
gawk-3.1.5-14.el5
#</pre>

But let&#8217;s say the file, program or library you are searching is not installed on your server. The rpm command will not be useful in that situation, this is when the &#8221; yum&#8221;  command can help us. In the example below, we are searching for the program &#8220;compress&#8221;. We will use the &#8220;yum&#8221; command with the &#8220;provides&#8221; option and then the &#8220;\*/compress&#8221; parameters. By specifying the &#8220;\*/compress&#8221;, we are telling &#8220;yum&#8221; to display matching file that end with &#8221; compress&#8221;  and that it could in any (*) directory. I am sure we will use this command often from now on, right ?

<pre># <span style="color: #0000ff;"><strong>yum provides */compress</strong> </span>  <strong>(We do not have to know where the program is or will be located)</strong>
Loaded plugins: changelog, fastestmirror, priorities
Loading mirror speeds from cached hostfile
 * base: centos.mirror.iweb.ca
 * updates: centos.mirror.iweb.ca
 * addons: centos.mirror.iweb.ca
 * extras: centos.mirror.iweb.ca
<span style="color: #0000ff;"><strong>ncompress-4.2.4-47.i386</strong></span> : Fast compression and decompression utilities.
Matched from:
Filename    : /usr/bin/compress
#</pre>

### <span style="color: #000000;"><strong>Using yum to download package without installing them<br /> </strong></span>

There are situation when you only need to download the package without installing it. By using the option &#8220;&#8211;downloadonly&#8221; of the &#8220;yum install&#8221; command, we can do just that.

But first, we need to install the plugins &#8220;yum-downloadonly&#8221; to add that functionality to the &#8220;yum&#8221; command.

<pre># <strong><span style="color: #0000ff;">yum install -y yum-downloadonly</span></strong></pre>

After installing the plugin, when ever we need to download a package without installing it, we can use the &#8220;&#8211;downloadonly&#8221; option of the &#8220;yum install&#8221; command to do it. By default, the package will be downloaded in the directory &#8220;/var/cache/yum/base/packages&#8221;. If you need to download the package in a particular directory, you can specify it with the &#8220;&#8211;downloaddir&#8221; option (example below).

<pre># <span style="color: #0000ff;"><strong>yum install -y --downloadonly --downloaddir=/tmp zsh</strong></span>
Loaded plugins: changelog, downloadonly, rhnplugin
Setting up Install Process
Parsing package install arguments
Resolving Dependencies
--&gt; Running transaction check
---&gt; Package zsh.i386 0:4.2.6-3.el5 set to be updated
--&gt; Finished Dependency Resolution

Dependencies Resolved
==================================================================
 Package         Arch       Version       Repository         Size
==================================================================
Installing:
 zsh             i386      4.2.6-3.el5  rhel-i386-server-5   1.7 M
Transaction Summary
==================================================================
Install      1 Package(s)
Update       0 Package(s)
Remove       0 Package(s)
Total size: 1.7 M
Downloading Packages:
<span style="color: #0000ff;"><strong>exiting because --downloadonly specified</strong></span>
#
# <strong><span style="color: #0000ff;">ls -l /tmp/zsh*</span></strong>
-rw-r--r-- 1 root root 1787914 May  6 11:31 <span style="color: #0000ff;">/tmp/zsh-4.2.6-3.el5.i386.rpm</span>
#</pre>

### **Installing a local rpm and resolving dependencies**

Let&#8217;s say you have a rpm package on your server that you want to install. You have tried to install it with the regular &#8220;rpm&#8221; install command, but the package have some rpm dependencies that you do not have. You can then use the &#8220;yum localinstall&#8221; command to install your package and let &#8220;yum&#8221; try to resolve these dependencies. For example, we have a package named &#8220;shutter&#8221;, we cannot install it with the regular rpm command because it has a lot of dependencies. Below, we use to &#8220;yum localinstall&#8221; to resolve them. The example below represent only a partial screen output.

<pre>#<strong><span style="color: #0000ff;"> ls -l shutter-0.70.1-1.ppa2.fc10.noarch.rpm </span></strong>
-rw-rw-r-- 1 jacques jacques 780953 Sep 19 12:02 shutter-0.70.1-1.ppa2.fc10.noarch.rpm
# 

#<strong><span style="color: #0000ff;"> yum localinstall shutter-0.70.1-1.ppa2.fc10.noarch.rpm </span></strong>
Loaded plugins: downloadonly, fastestmirror
Setting up Local Package Process
Examining shutter-0.70.1-1.ppa2.fc10.noarch.rpm: shutter-0.70.1-1.ppa2.fc10.noarch
Marking shutter-0.70.1-1.ppa2.fc10.noarch.rpm to be installed
Loading mirror speeds from cached hostfile
 * extras: mirrors.portafixe.com
 * base: mirrors.portafixe.com
 * updates: mirrors.portafixe.com
 * addons: mirrors.portafixe.com
<span style="color: #0000ff;">Resolving Dependencies</span>
--&gt; Running transaction check
---&gt; Package shutter.noarch 0:0.70.1-1.ppa2.fc10 set to be updated
--&gt; Processing Dependency: gnome-web-photo for package: shutter
--&gt; Processing Dependency: gtklp for package: shutter
--&gt; Processing Dependency: perl(Gnome2) for package: shutter
--&gt; Processing Dependency: perl(Gnome2::Canvas) for package: shutter
--&gt; Processing Dependency: perl(Gnome2::GConf) for package: shutter
--&gt; Processing Dependency: perl(Gnome2::VFS) for package: shutter
--&gt; Processing Dependency: perl(Gnome2::Wnck) for package: shutter
--&gt; Processing Dependency: perl(Goo::Canvas) for package: shutter
--&gt; Processing Dependency: perl(Gtk2) for package: shutter
....
....</pre>

### **Listing packages**

With the &#8220;list&#8221; parameter as you can guess, we will obtain a list of the packages available. With no parameter, the &#8220;list&#8221; will list all packages available. On the last column displayed, we will notice that it will informed us if the _package is actually **<span style="color: #0000ff;">installed</span>** or there is an **<span style="color: #0000ff;">update</span>**_ for it and _if it is not installed it will display the **<span style="color: #0000ff;">repository that offer the package</span>**_.

<pre>root@rhel5:~# <strong><span style="color: #0000ff;">yum list | grep -i openssh or yum list openssh*</span></strong>
openssh.i386                              4.3p2-29.el5                 <span style="color: #0000ff;">installed</span>
openssh-askpass.i386                      4.3p2-29.el5                 <span style="color: #0000ff;">installed</span>
openssh-clients.i386                      4.3p2-29.el5                 <span style="color: #0000ff;">installed</span>
openssh-server.i386                       4.3p2-29.el5                 <span style="color: #0000ff;">installed</span>

root@rhel5:~# <strong><span style="color: #0000ff;">yum list | grep -i openswan</span></strong>
openswan.i386                             2.6.14-1.el5_3.3            <span style="color: #0000ff;"> updates</span>
openswan-doc.i386                         2.6.14-1.el5_3.3             <span style="color: #0000ff;">updates</span>

root@rhel5:~# <strong><span style="color: #0000ff;">yum list | grep -i openvpn</span></strong>
NetworkManager-openvpn.i386               1:0.7.0-18.svn11.el5.1       <span style="color: #0000ff;">epel</span>
openvpn.i386                              2.1-0.29.rc15.el5            <span style="color: #0000ff;">epel</span>
openvpn-auth-ldap.i386                    2.0.3-3.el5                  <span style="color: #0000ff;">epel</span>
root@rhel5:~#</pre>

### **S****earch for pattern in all packages name, description and summary**

The search option checks the names, descriptions, summaries and listed package maintainers of all of the available packages to find those that match. For example, to search for all packages that relate to &#8220;zip&#8221;, type:

<pre># <strong><span style="color: #0000ff;">yum search zip</span></strong>
Loaded plugins: downloadonly, fastestmirror
Loading mirror speeds from cached hostfile
 * extras: mirrors.portafixe.com
 * base: mirrors.portafixe.com
 * updates: mirrors.portafixe.com
 * addons: mirrors.portafixe.com
======================= Matched: zip =================================================
mc.i386 : User-friendly text console file manager and visual shell
kdeutils.i386 : K Desktop Environment - Utilities
bzip2.i386 : A file compression utility.
bzip2-devel.i386 : Header files and libraries for developing apps which will use bzip2.
bzip2-libs.i386 : Libraries for applications using bzip2
eject.i386 : A program that ejects removable media using software control.
gzip.i386 : The GNU data compression program.
java-1.4.2-gcj-compat-src.i386 : Source files for libgcj
libgsf.i386 : GNOME Structured File library
mikmod.i386 : A MOD music file player.
mono-core.i386 : The Mono CIL runtime, suitable for running .NET code
ncompress.i386 : Fast compression and decompression utilities.
perl-Archive-Tar.noarch : A module for Perl manipulation of .tar files
perl-Archive-Zip.noarch : Perl library for accessing Zip archives
perl-Compress-Zlib.i386 : A module providing Perl interfaces to the zlib compression library.
perl-String-CRC32.i386 : Perl interface for cyclic redundency check generation
rar.i386 : RAR archiver to create and manage RAR archives
unzip.i386 : A utility for unpacking zip files.
xarchiver.i386 : Archive manager for Xfce
zip.i386 : A file compression and packaging utility compatible with PKZIP.
zlib.i386 : The zlib compression and decompression library.
zlib-devel.i386 : Header files and libraries for Zlib development.
[root@boris packages]
#</pre>

We can use any of the standard wildcard characters to run any search option with a partial word or name: ? to represent any one character, and * to mean zero or more characters. Always add the escape character (\) before wildcards.

### **Getting information about a package**

The &#8220;info&#8221; parameter allow you to display information about the package you specified.

<pre># <strong><span style="color: #0000ff;">yum info python</span></strong>
Loaded plugins: downloadonly, fastestmirror
Loading mirror speeds from cached hostfile
 * extras: mirrors.portafixe.com
 * base: mirrors.portafixe.com
 * updates: mirrors.portafixe.com
 * addons: mirrors.portafixe.com
Installed Packages
Name       : python
Arch       : i386
Version    : 2.4.3
Release    : 24.el5_3.6
Size       : 21 M
Repo       : installed
Summary    : An interpreted, interactive, object-oriented programming language.
URL        : http://www.python.org/
License    : PSF - see LICENSE
Description:
Python is an interpreted, interactive, object-oriented programming language often compared to Tcl, Perl, Scheme or Java. Python
includes modules, classes, exceptions, very high level dynamic data types and dynamic typing. Python supports interfaces to many
system calls and libraries, as well as to various windowing systems (X11, Motif, Tk, Mac and MFC).  Programmers can write new
built-in modules for Python in C or C++. Python can be used as an extension language for applications that need a programmable
interface. This package contains most of the standard Python modules, as well as modules for interfacing to the Tix widget set for
Tk and RPM.  Note that documentation for Python is provided in the python-docs package.
#</pre>

### **Correcting yum operation error  
** 

You may sometime encounter situation when trying to update your system, you keep getting error. This can occur, when download have been have been terminated abnormally. The first thing we should do in that situation is to run the &#8220;yum clean all&#8221; command. This will clear the yum cache directory (/var/cache/yum) and most of the time it resolve the problem.

<pre># <strong><span style="color: #0000ff;">yum clean all</span></strong>
Loaded plugins: downloadonly, fastestmirror
Cleaning up Everything
Cleaning up list of fastest mirrors
#</pre>

### **Listing package group**

This &#8220;grouplist&#8221; option, list the package group that are installed on your server and that are available for you to download. The example below was run on a Centos 5 server, on a Redhat server some package group may not show. Centos have more package group than Red hat. The list have also been shortened, to take less space on this page.

<pre># <span style="color: #0000ff;"><strong>yum grouplist</strong></span>
<strong><span style="color: #0000ff;">Installed Groups:</span></strong>
   Administration Tools
   DNS Name Server
   FTP Server
   Mail Server
   Multimedia
   MySQL Database
   ...
   Text-based Internet
   Web Server
   Windows File Server
   X Software Development
   X Window System
<span style="color: #0000ff;"><strong>Available Groups:</strong></span>
   Development Tools
   GNOME Software Development
    ...
   News Server
   Web Development
   Window Managers
Done
#</pre>

### <span style="color: #000000;"><strong>Getting information about a package group</strong></span>

The &#8220;groupinfo&#8221; option of the &#8220;yum&#8221; command display the list the packages that are part of the specified group. The &#8220;**Default Packages**&#8221; section, list the packaged that will be installed when that group is install. The &#8220;**Mandatory**&#8221; section, list packages that requires to be installed for the package group to work. Depending on the group you specify, there may be a &#8220;**Optional Packages**&#8221; section in the list, these packages are the part of the group but won&#8217;t be install by default, you will need to install them one by one should you require one of them.

<pre># <span style="color: #0000ff;"><strong>yum groupinfo Graphics"</strong></span>
[root@boris ~]# yum groupinfo "DNS Name Server"
Loaded plugins: downloadonly, fastestmirror
Setting up Group Process
Loading mirror speeds from cached hostfile
 * extras: centos.mirror.iweb.ca
 * base: centos.mirror.iweb.ca
 * updates: centos.mirror.iweb.ca
 * addons: centos.mirror.iweb.ca

Group: DNS Name Server
 Description: This package group allows you to run a DNS name server (BIND) on the system.
 <strong>Mandatory Packages</strong>:
   bind
 <strong>Default Packages</strong>:
   bind-chroot
#</pre>

### <span style="color: #000000;"><strong><span style="color: #000000;">Installing a package group </span></strong></span>

As you may already guess, the &#8220;groupinstall&#8221; option allow you to install the default (and required if needed) of the specified group. In the example below, we have install the &#8220;DNS Name Server&#8221; group.

<pre>root@anemone:/# <span style="color: #0000ff;"><strong>yum groupinstall "DNS Name Server"</strong></span>
Package 30:bind-9.3.4-10.P1.el5_3.3.i386 already installed and latest version
Resolving Dependencies
--&gt; Running transaction check
---&gt; Package bind-chroot.i386 30:9.3.4-10.P1.el5_3.3 set to be updated
--&gt; Finished Dependency Resolution
Dependencies Resolved
===============================================
 Package      Arch    Version
===============================================
Installing:
 bind-chroot  i386   30:9.3.4-10.P1.el5_3.3     

Transaction Summary
===============================================
Install      1 Package(s)
Update       0 Package(s)
Remove       0 Package(s)         

Total download size: 42 k
Is this ok [y/N]: y
Downloading Packages:
bind-chroot-9.3.4-10.P1.el5_3.3.i386.rpm                        |  42 kB     00:00
Running rpm_check_debug
Running Transaction Test
Finished Transaction Test
Transaction Test Succeeded
Running Transaction
  Installing     : bind-chroot                                       [1/1]
Installed: bind-chroot.i386 30:9.3.4-10.P1.el5_3.3
Complete!
#</pre>