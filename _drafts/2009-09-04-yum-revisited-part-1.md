---
id: 522
title: Yum Revisited (Part 1)
date: 2009-09-04T11:01:27+00:00
author: jacques
layout: post
guid: http://linternux.com/?p=522
permalink: /index.php/2009/09/04/yum-revisited-part-1/
categories:
  - Admin
tags:
  - Admin
  - rpm
  - yum
---
[<img loading="lazy" class="alignleft size-full wp-image-680" title="Yum" src="http://192.168.1.88/wp-content/uploads/2009/07/Yum.png" alt="Yum" width="185" height="70" />](http://192.168.1.88/wp-content/uploads/2009/07/Yum.png)This is a first of a series of two articles dedicated to the &#8220;yum&#8221; program. In this first article, we will mainly look at the configuration files. In the next article, we will look at some advance usage, that will help us in administrating our servers.  As always, will build our article, in a way that we can come back to it and use it as a reference if we need some information about &#8220;yum&#8221;. We will concentrate on the command line version of &#8220;yum&#8221;, but I would like mention that there is a graphical version called &#8220;yumex&#8221; that I would recommend. It is not the fastest one, but it is simple to use and it work.  Visit the  [&#8220;yumex&#8221; home page](http://www.yum-extender.org/blog/ "yumex home page"), you can aloso [download the RedHat/Centos 5 rpm](http://linternux.com/download/yumex/yumex-2.0.5-3.el5.noarch.rpm "Download yumex rpm") from here.

### Main configuration file (/etc/yum.conf)

We can say that &#8220;yum&#8221; configuration break in two parts, the main configuration file yum.conf  located in the /etc directory and the /etc/yum.repos.d directory that contains separated files for each repository that &#8220;yum&#8221; can connect to, for updating/installing our system. Below is a modified version of the yum.conf file that include comment for every option line, so we could use it as a reference. I think all options, along  with the comments included are self explanatory .

<pre># The Main section must exist for yum to do anything
<strong><span style="color: #0000ff;">[main]</span></strong>

# Directory where packages are downloaded
<span style="color: #0000ff;"><strong>cachedir=/var/cache/yum</strong></span>

# 0=Remove packages & headers after they are install/update
# 1=Keep downloaded packages after update
<span style="color: #0000ff;"><strong>keepcache=0</strong></span>

# Debug Level. Valid numbers are 0-10, default is 2
<strong><span style="color: #0000ff;">debuglevel=2</span></strong>

# Where yum log file is located
<span style="color: #0000ff;"><strong>logfile=/var/log/yum.log</strong></span>

# Distribution Package Name (Fedora=fedora-release, RedHat/Centos=redhat-release)
<span style="color: #0000ff;"><strong>distroverpkg=redhat-release</strong></span>

# 1=Complaint when package aleady install is installed, 0=not tolerant
# For example, if you request to install foo, bar and baz and baz is installed;
# yum won't error out complaining that baz is already installedt
<span style="color: #0000ff;"><strong>tolerant=1</strong></span>

# 1=Only update the architectures of packages that is installed, 0=Override Check
<span style="color: #0000ff;"><strong>exactarch=1</strong></span>

# Let yum determine if package is obsolete during update (1=Activate 0=Deactivate)
<span style="color: #0000ff;"><strong>obsoletes=1</strong></span>

# Confirms that all packages are cryptographically verified
<span style="color: #0000ff;"><strong>gpgcheck=1</strong></span>

# 1=Enabling yum plugins, 0=Disable yum plugins
# Once plugins are enabled in yum.conf, you may install yum plugins and use them.
<span style="color: #0000ff;"><strong>plugins=1</strong></span>

# Determine when repositories metedata expire after it is received.
# Note: yum-RHN-plugin doesn't honor this.
<span style="color: #0000ff;"><strong>metadata_expire=1h</strong></span>

# Some package (like kernel) can only be install and not updated
# So here we would keep 5 versions of the kernel.
# When the sixth one is installed, the oldest one will get deleted.
<span style="color: #0000ff;"><strong>installonly_limit = 5
</strong></span>
# List of packages to exclude from updates or installs.
# Uncomment below if you want to autoupdate also the kernel rpms
# Reboot is necessary to use the new kernel, once installed.
#<span style="color: #0000ff;"><strong> exclude=kernel*</strong></span>

# PUT YOUR REPOS HERE OR IN separate files named file.repo
# in /etc/yum.repos.d
#</pre>

### <!--more-->

### Yum repository directory (/etc/yum.repos.d)

In the &#8220;/etc/yum.repos.d&#8221;  directory, we will find one file for each repository we want to use to download or install packages. In a simple RedHat installation we will have one file, name &#8220;rhel-debuginfo.repo&#8221;. You can find the content and description of this file below. RedHat standard repository seems to be hidden or hard coded somewhere (check /etc/yum directory). Centos on the other side, have explicitly place 2 files in this directory (CentOS-Base.repo and CentOS-Media.repo), one for installing and updating our system from internet and the other one from CD/DVD.

<pre><strong># Section title - Can have multiple sections in the file
</strong><span style="color: #0000ff;">[rhel-debuginfo]</span>

<strong># Friendly name for the repository</strong>
<span style="color: #0000ff;">name=Red Hat Enterprise Linux $releasever - $basearch - Debug</span>

<strong># Full URL to the packages (Can be http:// frp:// or file://)</strong>
<span style="color: #0000ff;">Baseurl=ftp://ftp.redhat.com/pub/redhat/linux/enterprise/$releasever/en/os/$basearch/Debuginfo/</span>

<strong># Activate(1) or De-Activate(0) the repositor</strong>y
<span style="color: #0000ff;">enable=1</span>

<strong># Enable(1)/Disable(0) - Packages cryptographic check</strong>
<span style="color: #0000ff;">gpgcheck=1 </span>

<strong># GPG key </strong><span style="color: #000000;"><strong>used to verify RPM packages built and signed by Red Hat/Centos /Fedora</strong>
<span style="color: #0000ff;">gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-releas</span>e
</span></pre>

There are many additional repositories that we can subscribe for our RedHat/Centos/Fedora server. For example, there is the &#8220;[Extra Packages for Enterprise Linux (EPEL)](https://fedoraproject.org/wiki/EPEL "EPEL Repository")&#8220;, you can find the [procedure for subscribing to it](https://fedoraproject.org/wiki/EPEL/FAQ#howtouse "Hot-to subscribe to EPEL"), but basically you download and install the [RHEL4](http://download.fedora.redhat.com/pub/epel/4/i386/repoview/epel-release.html "EPEL RHEL 4 Packages Page") and [RHEL5](http://download.fedora.redhat.com/pub/epel/5/i386/repoview/epel-release.html "EPEL RHEL 5 Packages Page") rpm. There is also the [RPMforge repository](https://rpmrepo.org/RPMforge "RPM Forge Site") where you will find packages for RedHat/Centos. Download the [RedHat 5/Centos 5 rpm](http://packages.sw.be/rpmforge-release/rpmforge-release-0.3.6-1.el5.rf.i386.rpm "RedHat/Centos5 32 Bits RPM") or the [RedHat 4/Centos 4 rpm](http://packages.sw.be/rpmforge-release/rpmforge-release-0.3.6-1.el4.rf.i386.rpm "RedHat/Centos4 32 Bits RPM") and follow the same procedure describe above for EPEL repository. There is also the [DAG site](http://dag.wieers.com/rpm/ "Dag rpm site") and you can [subscribe to the repository from this page](http://dag.wieers.com/rpm/FAQ.php#B "Download Subscribing page").

**Example, subscribing to EPEL RedHat 5 repository ;**

<pre><span style="color: #0000ff;"><strong>GPG Repositories keys before subscribing</strong>
</span>root@rhel5:/etc/yum.repos.d# ls -l /etc/pki/rpm-gpg/
total 16
-rw-r--r-- 1 root root 1512 Mar 16 21:30 RPM-GPG-KEY-beta
-rw-r--r-- 1 root root 1504 Mar 16 21:30 RPM-GPG-KEY-CentOS-5

<span style="color: #0000ff;"><strong>Yum repository directory before</strong></span>
root@rhel5:/etc/yum.repos.d# ls -l
total 16
-rw-r--r-- 1 root root 2323 Mar 16 21:30 CentOS-Base.repo
-rw-r--r-- 1 root root  626 Mar 16 21:30 CentOS-Media.repo
root@rhel5:/etc/yum.repos.d#

<span style="color: #0000ff;"><strong>Subcribing to EPEL RedHat 5 repository</strong></span>
# rpm -Uvh http://download.fedora.redhat.com/pub/epel/5/i386/epel-release-5-3.noarch.rpm
Retrieving http://download.fedora.redhat.com/pub/epel/5/i386/epel-release-5-3.noarch.rpm
warning: /var/tmp/rpm-xfer.hoNaf4: Header V3 DSA signature: NOKEY, key ID 217521f6
Preparing...                ########################################### [100%]
 1:epel-release           ########################################### [100%]

<span style="color: #0000ff;"><strong>Yum repository directory after subscribing</strong></span>
root@rhel5:/etc/yum.repos.d# ls -l
total 24
-rw-r--r-- 1 root root 2323 Mar 16 21:30 CentOS-Base.repo
-rw-r--r-- 1 root root  626 Mar 16 21:30 CentOS-Media.repo
-rw-r--r-- 1 root root  954 Apr 25  2008 <span style="color: #0000ff;">epel.repo</span>
-rw-r--r-- 1 root root 1054 Apr 25  2008 <span style="color: #0000ff;">epel-testing.repo
</span>
<span style="color: #0000ff;"><strong>GPG Repositories keys after subscribing</strong></span>
root@rhel5:/etc/yum.repos.d# ls -l /etc/pki/rpm-gpg/
total 20
-rw-r--r-- 1 root root 1512 Mar 16 21:30 RPM-GPG-KEY-beta
-rw-r--r-- 1 root root 1504 Mar 16 21:30 RPM-GPG-KEY-CentOS-5
-rw-r--r-- 1 root root 1698 Apr  3  2007 <span style="color: #0000ff;">RPM-GPG-KEY-EPEL
</span>root@rhel5:/etc/yum.repos.d#</pre>

Always be careful when you subscribe to a new repositories, you may encounter problem on some update afterward. If you do use additional repositories, I would suggest that you activate it to download the needed packages and then deactivate it by changing the line &#8220;enable=1&#8221; to &#8220;enable=0&#8221;, in the proper file in /etc/yum.repos.d directory. If you ever need again, just activate the &#8220;enable&#8221; line.

In the next article, we will focus on the using the &#8220;yum&#8221; command itself, see you soon.