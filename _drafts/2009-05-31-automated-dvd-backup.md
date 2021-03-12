---
id: 378
title: Automated DVD Backup
date: 2009-05-31T21:52:48+00:00
author: jacques
layout: post
guid: http://linternux.com/?p=378
permalink: /index.php/2009/05/31/automated-dvd-backup/
categories:
  - Scripting
tags:
  - dvd
  - script
  - Tools
---
Today I would like to share with you, a little script that I use every week. I have four rewritable DVD that I use in rotation to backup important directories of my server at home. All I have to do is to leave one of these DVD+RW in the dvd drive and a backup of my important directories will be taken at regular interval. I have schedule a cron job that run tree times a week, to backup to DVD directories that are important to me. This is by no way a replacement of a full system backup like [Mondo rescue](http://www.mondorescue.org/), [Storix](http://www.storix.com/) or [Acronis](http://www.acronis.com/enterprise/products/ATISLin/), but they represent to me the directories that are subject to change more often. I must specify that the command used in this **script require using a DVD+RW to work**.

<!--more--> The script need some tools included in the &#8216;dvd+rw-tools&#8221; package, just be sure you install it before running the script

<pre><strong>Fedora or RHEL 5
# yum install 'dvd+rw-tools'
RedHat Enterprise 3 or 4
# up2date 'dvd+rw-tools'
</strong></pre>

Now, let&#8217;s schedule our script to run every Monday, Wednesday and Friday at 3am. I have chosen to place the script in a directory (filesystem) name /linternux in the sub-directory &#8216;bin&#8217;, but you can place it where ever you like. For those more familiar with Linux, you can use the çrontab -e&#8217; command and add the two lines presented here at the end of file. Below, I am presenting an alternative method to add the lines in the &#8216;crontab&#8217; file of user &#8216;root&#8217;. When you add lines to the &#8216;root&#8217; crontab file in this matter, you must inform the &#8216;cron&#8217; daemon that it need to reload its configuration file, so it is aware of the changes we made. Both of these method, will schedule our script at the chosen time.

<pre><strong># echo '# Backup important directories to DVD' &gt;&gt; /var/spool/cron/root
# echo '0 3 * * 1,3,5 /linternux/bin/dvd_backup.sh &gt;/dev/null 2&gt;&1' &gt;&gt; /var/spool/cron/root
# service crond reload
Reloading cron daemon configuration:                       [  OK  ]
#</strong></pre>

Once downloaded the [dvd_backup.sh](http://linternux.com/download/dvd_backup.sh "Download Script") script, need to modify it to fit your need.

  * &#8216;<span style="color: #0000ff;"><strong>BASEDIR</strong></span>&#8216; variable need to be change to represent the location of the &#8216;log&#8217; directory. This can easily be change to &#8216;BASEDIR=&#8221;/var&#8221;&#8216;. This is where the log file of the script is created. This is the file that will be email to you.
  * &#8220;**<span style="color: #0000ff;">DVD_DEV</span>**&#8221; indicate the device representing your DVD writer. Usually, it should be set to &#8216;/dev/cdrom&#8217;, &#8216;dev/hdd&#8217; or &#8216;/dev/dvd&#8217;.
  * &#8220;**<span style="color: #0000ff;">SYSADMIN</span>**&#8221; variable represent the email address you would like the log file to be sent.
  * &#8220;**<span style="color: #0000ff;">BACKDIR</span>**&#8221; is the variable where you specify each and every directories you wish to include on the DVD backup. The syntax may seems redundant, but this is the way we need to specify them. We need to specify the name of the directory twice, separated by and equal sign.<figure style="width: 576px" class="wp-caption aligncenter">

**<img loading="lazy" title="dvd_backup.sh - Figure 1" src="http://linternux.com/images/dvd_backup_figure1.png" alt="Part that is subject to change " width="576" height="527" />**<figcaption class="wp-caption-text">Part that is subject to change </figcaption></figure> 

The rest of the script does not need to be changed, but is presented here for information purpose only. <span style="text-decoration: underline;">The script require a DVD+RW, the DVD-RW will not work with this script</span>. The script use the &#8220;dvd+rw-format&#8221; and the &#8220;growisofs&#8221; command to format and write to the DVD. If an error occurs, during the format or the backup, an email is sent to the system administrator and the process is stopped.<figure style="width: 991px" class="wp-caption aligncenter">

**<img loading="lazy" title="dvd_backup_figure2" src="http://linternux.com/images/dvd_backup_figure2.png" alt="Part of the script that doesnt need to be change" width="991" height="535" />**<figcaption class="wp-caption-text">Part of the script that doesn't need to be change</figcaption></figure> 

If you want to know more about using dvd+rw , see the &#8220;[Burning DVD+RW in Linux](http://www.physics.ucsb.edu/~pcs/apps/linux_dvdr_burning.html)&#8221; web page.

The script [dvd_backup.sh](http://linternux.com/download/dvd_backup.sh) is free to use, have fun and see you soon !