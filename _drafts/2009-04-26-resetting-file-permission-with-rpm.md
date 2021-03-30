---
id: 99
title: Resetting file permission with rpm
date: 2009-04-26T16:27:59+00:00
author: jacques
layout: post
guid: http://blog.linternux.com/?p=99
permalink: /index.php/2009/04/26/resetting-file-permission-with-rpm/
categories:
  - Admin
tags:
  - rpm
---
Did you know that you can reset file permission to what they were when you installed the package. This may become helpful if somebody made a mistake and change the permission on a number of files (This never happen right?). This is where the command ;  
&#8220;rpm &#8211;setperms <package>&#8221; become handy.

In our example we will change the permission on the &#8220;find&#8221; command.

<pre># Let's find out what package contains the "/usr/bin/find" command
# <strong>rpm -qf /usr/bin/find
</strong>findutils-4.2.27-5.el5

# Let's see what are the actual permission of the find program (755).
# <strong>ls -l /usr/bin/find</strong>
<span style="color: #0000ff;"><strong>-rwxr-xr-x</strong></span> 1 root root 151244 Jul  7  2008 /usr/bin/find

# Let's change them to 777 with the chmod command
# <strong>chmod 777 /usr/bin/find</strong>

# Now the program "find" have this permission.
# <strong>ls -l /usr/bin/find</strong>
<span style="color: #0000ff;"><strong>-rwxrwxrwx</strong></span> 1 root root 151244 Jul  7  2008 /usr/bin/find

# Let's run our command to reset all files included in the package "findutils".
# <strong>rpm --setperms findutils</strong>

# You can see now that the permission of the 'find' are back to their initial state.
# <strong>ls -l /usr/bin/find</strong>
<strong><span style="color: #0000ff;">-rwxr-xr-x</span></strong> 1 root root 151244 Jul  7  2008 /usr/bin/find</pre>

Be aware that this command only reset the permission and not the owner/group of the file, as you can see from the example below.

<pre># <strong>ls -l /usr/bin/find</strong>
-rwxr-xr-x 1 root root 151244 Jul  7  2008 /usr/bin/find
# <strong>chmod 777 /usr/bin/find</strong>
# <strong>chown jacques.jacques /usr/bin/find</strong>
# <strong>ls -l /usr/bin/find</strong>
<strong><span style="color: #0000ff;">-rwxrwxrwx 1 jacques jacques </span></strong>151244 Jul  7  2008 /usr/bin/find
# <strong>rpm --setperms findutils</strong>
# <strong>ls -l /usr/bin/find</strong>
<strong><span style="color: #0000ff;">-rwxr-xr-x 1 jacques jacques</span></strong> 151244 Jul  7  2008 /usr/bin/find</pre>