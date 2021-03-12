---
id: 114
title: What script is run when a package is install ?
date: 2009-05-02T11:00:33+00:00
author: jacques
layout: post
guid: http://linternux.com/?p=114
permalink: /index.php/2009/05/02/what-script-is-run-when-a-rpm-is-installed/
categories:
  - Admin
tags:
  - rpm
---
Today, we will look at what script is run when you install a rpm. In our example we will look at the script that is run when we perform a kernel installation.  First, let&#8217;s look what kernel version is installed on our system, with the this command :

<pre><strong># rpm -qa | grep -i kernel</strong>
kernel-2.6.18-92.1.22.el5
kernel-2.6.18-92.1.18.el5
kernel-2.6.18-128.1.6.el5
kernel-headers-2.6.18-128.1.6.el5
#</pre>

Let&#8217;s view the script that is run when the kernel 2.6.18-128.1.6 is install ;

<pre><strong># rpm -q --scripts kernel-2.6.18-128.1.6.el5</strong>
postinstall scriptlet (using /bin/sh):
if [ `uname -i` == "x86_64" -o `uname -i` == "i386" ]; then
   if [ -f /etc/sysconfig/kernel ]; then
      /bin/sed -i -e 's/^DEFAULTKERNEL=kernel-smp$/DEFAULTKERNEL=kernel/' /etc/sysconfig/kernel || exit $?
   fi
fi
/sbin/new-kernel-pkg --package kernel --mkinitrd --depmod --install 2.6.18-128.1.6.el5 || exit $?
if [ -x /sbin/weak-modules ]
   then
   /sbin/weak-modules --add-kernel 2.6.18-128.1.6.el5 || exit $?
fi
preuninstall scriptlet (using /bin/sh):
/sbin/new-kernel-pkg --rminitrd --rmmoddep --remove 2.6.18-128.1.6.el5 || exit $?
if [ -x /sbin/weak-modules ]
   then
   /sbin/weak-modules --remove-kernel 2.6.18-128.1.6.el5 || exit $?
fi
#</pre>