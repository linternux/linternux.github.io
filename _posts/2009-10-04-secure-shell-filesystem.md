---
id: 824
title: Secure Shell Filesystem
date: 2009-10-04T11:11:18+00:00
author: jacques
layout: post
guid: http://linternux.com/?p=824
permalink: /index.php/2009/10/04/secure-shell-filesystem/
categories:
  - Network
  - Storage
tags:
  - Network
  - ssh
---
[<img loading="lazy" class="alignleft size-full wp-image-974" title="sshfs" src="http://192.168.1.88/wp-content/uploads/2009/10/sshfs.png" alt="sshfs" width="115" height="115" />](http://192.168.1.88/wp-content/uploads/2009/10/sshfs.png)In this article, we are looking at SSHFS, the Secure Shell Filesystem. We can use it to mount a remote filesystem using the SSH Protocol.  So the information flowing between the two systems is completely encrypted. SSHFS is a client based application, so beside the SSH server, there is nothing to installed on the remote server to use it.  FUSE is a linux kernel module that allow non-privilege user to mount their own filesystem without the help of any kernel code. One of the interesting feature of SSHFS is that you can securely mount a filesystem over the internet, this is impossible with Samba and not very secure with NFS. If you like more information on SSHFS, you can visit the  [SSHFS homepage](http://fuse.sourceforge.net/sshfs.html "Secure Shell Filesystem Home Page") and the [Wiki](http://en.wikipedia.org/wiki/SSHFS "Secure Shell Filesystem Wiki ") of the SSHFS package. There is also a [YouTube video](http://www.youtube.com/watch?v=E5BDkadiaZo "YouTube Video about SSHFS") that show how to use SSHFS on a Fedora system. For those interested in a windows version of sshfs, there is a free version available at the [Dokan site](http://dokan-dev.net/en/), you need to install the Dokan Library first, then install SSHFS. I have done some simple test with it and I didn&#8217;t had any problem.

### Installing fuse and fuse-sshfs

FUSE and FUSE-SSHFS use the ssh protocol, so SSH needs to be installed on our client system. The only required package on the remote system,  is &#8220;openssh&#8221; (may work with other version of ssh). The package FUSE and FUSE-SSHFS  don&#8217;t need to be install on the remote system, only on the local server.  The first thing we  need to do is to get the [latest version the &#8220;fuse&#8221; at this page](http://dag.wieers.com/rpm/packages/fuse/) and [&#8220;fuse-sshfs&#8221; packages from this page](http://dag.wieers.com/rpm/packages/fuse-sshfs/ "Page to download fuse-sshfs") and install them on our local system. The package &#8220;fuse&#8221; is now part of the RedHat/Centos 5.4, but you still need to get &#8220;fuse-sshfs&#8221; cause it isn&#8217;t included. Should the other site be unresponsive, you can download the rpm from Linternux site.  


<center>
  </p> <table align=center> 
  
  <tr>
    <td>
      RedHat/Centos 5
    </td>
    
    <td>
      <a title="fuse for RedHat/Centos 5" href="http://linternux.com/download/sshfs/fuse-2.7.4-1.el5.rf.i386.rpm">fuse-2.7.4-1.el5.rf.i386.rpm</a>
    </td>
    
    <td>
      <a title="fuse-sshfs for RedHat/Centos 5" href="http://linternux.com/download/sshfs/fuse-sshfs-2.2-5.el5.i386.rpm">fuse-sshfs-2.2-5.el5.i386.rpm</a>
    </td>
  </tr>
  
  <tr>
    <td>
      RedHat/Centos 4
    </td>
    
    <td>
      <a title="fuse for RedHat/Centos 4" href="http://linternux.com/download/sshfs/fuse-2.7.4-1.el4.rf.i386.rpm">fuse-2.7.4-1.el4.rf.i386.rpm</a>
    </td>
    
    <td>
      <a title="fuse-sshfs for RedHat/Centos 4" href="http://linternux.com/download/sshfs/fuse-sshfs-2.2-1.el4.rf.i386.rpm">fuse-sshfs-2.2-1.el4.rf.i386.rpm</a>
    </td>
  </tr>
  
  <tr>
    <td>
      RedHat/Centos 3
    </td>
    
    <td>
      <a title="fuse for RedHat/Centos 3" href="http://linternux.com/download/sshfs/fuse-2.7.4-1.el3.rf.i386.rpm">fuse-2.7.4-1.el3.rf.i386.rpm</a>
    </td>
    
    <td>
      <a title="fuse-sshfs for RedHat/Centos 3" href="http://linternux.com/download/sshfs/fuse-sshfs-2.2-1.el3.rf.i386.rpm"> fuse-sshfs-2.2-1.el3.rf.i386.rpm</a>
    </td>
  </tr>
  
  <tr>
    <td>
      Fedora 10
    </td>
    
    <td>
      <a title="fuse for Fedora 10" href="http://linternux.com/download/sshfs/fuse-2.7.4-2.fc10.i386.rpm">fuse-2.7.4-2.fc10.i386.rpm</a>
    </td>
    
    <td>
      <a title="fuse-sshfs for Fedora 10" href="http://linternux.com/download/sshfs/fuse-sshfs-2.2-5.fc10.i386.rpm">fuse-sshfs-2.2-5.fc10.i386.rpm</a>
    </td>
  </tr>
  
  <tr>
    <td>
      Fedora 11
    </td>
    
    <td>
      <a title="fuse for Fedora 11" href="http://linternux.com/download/sshfs/fuse-2.7.4-3.fc11.i586.rpm">fuse-2.7.4-3.fc11.i586.rpm</a>
    </td>
    
    <td>
      <a title="fuse-sshfs for Fedora 11" href="http://linternux.com/download/sshfs/fuse-sshfs-2.2-2.fc11.i586.rpm">fuse-sshfs-2.2-2.fc11.i586.rpm</a>
    </td>
  </tr></table> 
  
  <p>
    </center>
  </p>
  
  <pre># <span style="color: #0000ff;"><strong>ls -l</strong></span>
total 296
-rw-rw-r-- 1 jacques jacques 255149 Sep  6 13:15 <span style="color: #0000ff;"><strong>fuse-2.7.3-1.el5.rf.i386.rpm</strong></span>
-rw-rw-r-- 1 jacques jacques  43203 Sep  6 13:15 <strong><span style="color: #0000ff;">fuse-sshfs-1.9-1.el5.rf.i386.rpm</span>
</strong>
# <span style="color: #0000ff;"><strong>rpm -ivh fuse*</strong></span>
warning: fuse-2.7.3-1.el5.rf.i386.rpm: Header V3 DSA signature: NOKEY, key ID 6b8d79e6
Preparing...             ########################################### [100%]
1:fuse                   ########################################### [ 50%]
2:fuse-sshfs             ########################################### [100%]
#</pre>
  
  <h3>
    <!--more-->
  </h3>
  
  <h3>
    Mounting the filesystem
  </h3>
  
  <p>
    You will notice that all of the commands we used below are done with a normal user account, you can use &#8220;root&#8221; if needed but I would recommend sticking with your user account.  Now we need to create our SSHFS mount point, <strong>it is important that the user that will do the sshfs mount be part of the group &#8220;fuse&#8221;</strong>, otherwise<strong> </strong> you will get this error message &#8220;fuse: failed to open /dev/fuse: Permission denied&#8221;. So, in the example below, we will create the mount point &#8220;remdir&#8221; in our home directory. We can then issue the &#8220;sshfs&#8221; command to log on the remote system with the user &#8220;jacques&#8221; and mount the remote directory named &#8220;/home/jacques&#8221;  on our local directory &#8220;/home/jacques/remdir&#8221;. The password of the user we selected will be prompted and then the sshfs filesystem will be mounted. To confirm that the filesystem is mounted, we issue the &#8220;df -h&#8221; command.
  </p>
  
  <pre>[jacques@local ~]$<span style="color: #0000ff;"><strong> pwd</strong></span>
<span style="color: #0000ff;">/home/jacques</span>
[jacques@local ~]$ <span style="color: #0000ff;"><strong>mkdir remdir</strong></span>

[jacques@local ~]$ <span style="color: #0000ff;"><strong>sshfs jacques@remote_host:/home/jacques /home/jacques/workdir</strong></span>
jacques@remote_host's password: xxxxxxxxxx

[jacques@local ~]$ <strong><span style="color: #0000ff;">df -h /home/jacques/remdir</span></strong>
Filesystem                                 Size  Used Avail Use%  Mounted on<strong>
<span style="color: #0000ff;">sshfs#jacques@remote_host:/home/jacques   1000G    0  1000G   0%  /home/jacques/remdir</span></strong>
[jacques@local ~]$</pre>
  
  <p>
    You will notice that the data of the filesystem presented by the &#8220;df -h&#8221; is wrong, this seems to be normal at this point and do not cause any problem. Every test I made, I would get the same value so don&#8217;t rely on these number. If you do not specify the remote directory, the HOME directory is use.
  </p>
  
  <h3>
    Unmounting the filesystem
  </h3>
  
  <p>
    When you need to unmount the filesystem, we will use the &#8220;fusermount&#8221; command with the &#8220;-u&#8221; parameter.
  </p>
  
  <pre>[jacques@local ~]$ <strong><span style="color: #0000ff;">fusermount -u /home/jacques/remdir</span></strong></pre>
  
  <p>
    Hope that you have found this article interesting. The Secure Shell Filesystem may become handy is some situation. So keep coming back, I have a lot of ideas for new articles (but not much time) and I hope to see you soon.
  </p>