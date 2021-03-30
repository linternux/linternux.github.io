---
id: 300
title: 'Add date &#038; time to your bash history'
date: 2009-05-24T10:57:48+00:00
author: jacques
layout: post
guid: http://linternux.com/?p=300
permalink: /index.php/2009/05/24/add-time-to-bash-history/
categories:
  - 'Tips &amp; Tricks'
tags:
  - history
---
We all know the &#8220;history&#8221; command that display the list of commands you previously entered.  But have you ever wish to know when these commands were entered ?  This useful tip, will require only  a few seconds to put in place and will now add the date and time in your history file.<figure style="width: 479px" class="wp-caption alignnone">

<img loading="lazy" title="Usual history command output" src="http://linternux.com/images/history_command_figure_1.png" alt="Output of history command before the change to .bashrc" width="479" height="241" /> <figcaption class="wp-caption-text">Output of history command before the change to /etc/bashrc</figcaption></figure> 

<!--more-->

Let&#8217;s modify our system global &#8220;bashrc&#8221; file in /etc, so that the change affect every users using the system.<figure style="width: 537px" class="wp-caption alignnone">

<img loading="lazy" title="Adding date and time in our history file" src="http://linternux.com/images/modify_bashrc.png" alt="Defining HISTTIMEFORMAT variable in /etc/bashrc" width="537" height="137" /> <figcaption class="wp-caption-text">Defining HISTTIMEFORMAT variable in /etc/bashrc</figcaption></figure> 

**Now, we need to log out and log back in the system to make our change effective.**<figure style="width: 509px" class="wp-caption alignnone">

<img loading="lazy" title="History command now show the date and time" src="http://linternux.com/images/history_command_figure_2.png" alt="History command now include the date & time for each command" width="509" height="332" /> <figcaption class="wp-caption-text">History command now include the date & time for each command</figcaption></figure> 

You will now notice,  the each command is preceded with a date and a time. Be aware, that all commands entered before our change to /etc/bashrc have been assigned the date and time we first login after doing the change.

So from now on every command we enter, will be recorded in .bash_history file with the date and time it was entered.

See you soon !