---
id: 434
title: Creating a PDF printer
date: 2009-06-06T20:58:50+00:00
author: jacques
layout: post
guid: http://linternux.com/?p=434
permalink: /index.php/2009/06/06/create-pdf-printer/
categories:
  - Printing
tags:
  - Printing
---
When I decided to move to Linux on my desktop, I tried to reproduce the same functionality that I was having under windows. One of the program I was using under Windows was [PDF-Factory](http://fineprint.com/pdf/) , it actually create a PDF printer. When ever I wanted to produced a PDF, I only had to print to that PDF printer. After some research, I found &#8220;cups-pdf&#8221;, this utility allow me to have the same functionality under Linux. In this article, I installed the product under RHEL5, but installing it on other version of Linux is very similar.

Once downloaded, installing cups-pdf is quite simple. Just type following command :

<pre><span style="color: #000080;"># </span><span style="color: #0000ff;"><strong><span style="color: #000080;">rpm -ivh cups-pdf-2.4.6-1.el5.i386.rpm</span>
</strong></span>warning: cups-pdf-2.4.6-1.el5.i386.rpm: Header V3 DSA signature: NOKEY, key ID 217521f6
Preparing...                ########################################### [100%]
1:cups-pdf               ########################################### [100%]
#</pre>

Next, we need to customize &#8220;cups-pdf&#8221; to our needs. To do that, we edit the configuration &#8220;/etc/cups/cups-pdf.conf&#8221;. By default every PDF you print will end up on your desktop. I choose to create a directory called &#8220;pdf&#8221; in my home directory and change cups-pdf configuration accordingly.

<pre># <span style="color: #000080;"><strong>vi /etc/cups/cups-pdf.conf</strong></span>
#Out ${HOME}/Desktop
<strong><span style="color: #000080;">Out ${HOME}/pdf</span></strong></pre>

By default file are created with a umask of 0077, so only I can read it. I change it to 0022 so that everybody can read it.

<pre>#UserUMask 0077
<strong><span style="color: #000080;">UserUMask 0002</span></strong></pre>

These are the only fields, I usually change before start using &#8220;cups-pdf&#8221;.

If you have any problem using &#8220;cups-pdf&#8221;, remove the comment on the &#8220;Log&#8221; and &#8220;Logtype&#8221; line to activate the logging. The configuration file is well documented, take a look at it and customize cups-pdf to it full extend.

You can download the version 2.4.6 for [RHEL4](http://linternux.com/download/cups/cups-pdf-2.4.6-2.el4.i386.rpm "Red Hat Enterprise Line 4 Package") and [RHEL5](http://linternux.com/download/cups/cups-pdf-2.4.6-1.el5.i386.rpm "Red Hat Enterprise Line 5 Package") or 2.5.0 for [Fedora 10](http://linternux.com/download/cups/cups-pdf-2.5.0-1.fc10.i386.rpm "Package for Fedora 10 ") and [Fedora 11](http://linternux.com/download/cups/cups-pdf-2.5.0-2.fc11.i586.rpm "Package for Fedora 11").

To download packages for other version of linux visit the [download page](http://www.cups-pdf.de/download.shtml "cups download page of home site.") of [cups-pdf home page](http://www.cups-pdf.de/welcome.shtml).

Happy printing !