---
layout: post
title: yum tips and tricks
date: 2009-05-04 11:26:07
categories:
  - sysadmin
tags:
  - yum
permalink: /index.php/yumtricks/
---

The &#8220;yum&#8221; is a powerful and useful command. We will present here some options of this command. Hopefully, you will find these tips helpful and that you learn something.

<span style="color: #0000ff;"><strong>Applying only security update</strong></span>

To be able to apply only security update on your server, we first need to install a plugin for this purpose. You can install it with this command:

    yum install yum-security

After that, we can use this plugin to update your computer to the latest security fixes :

    yum update --security
    
    

**<span style="color: #0000ff;">Applying all update except the kernel update</span>**

On some system, you may want to apply all update, except the one concerning the kernel.  To put this exception , add the following line to the file /etc/yum.conf ;

<pre>echo 'exclude=kernel*' &gt;&gt; /etc/yum.conf<a id="enabling_disabling_update_system" name="enabling_disabling_update_system"><strong><span style="color: #0000ff;">

</span></strong></a></pre>

<a id="enabling_disabling_update_system" name="enabling_disabling_update_system"><strong></strong></a>

<a id="enabling_disabling_update_system" name="enabling_disabling_update_system"><strong><span style="color: #0000ff;">Applying all update except the kernel update</span></strong></a>

In order to enable the automatic update system run following commands:

<pre class="code">chkconfig --add <span class="search_hit">yum</span>-autoupdate
service <span class="search_hit">yum</span>-autoupdate start</pre>

In order to disable the system run:

<pre class="code">service <span class="search_hit">yum</span>-autoupdate stop
chkconfig --del <span class="search_hit">yum</span>-autoupdate</pre>