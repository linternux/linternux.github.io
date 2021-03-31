---
title: Resetting file permission with rpm
date: 2021-03-29 10:34 
layout: post
categories:
  - SysAdmin 
tags:
  - rpm
  - permission
---

<br>
Did you know that you can reset file permission to what they were when you installed the package. This may become helpful if somebody made a mistake and change the permission on a number of files (This never happen right). 

<!--more-->

This is where the command &#8220;rpm &#8211;setperms <package>&#8221; become handy.

In our example we will change the permission on the &#8220;find&#8221; command.  
Let's find out what package contains the "/usr/bin/find" command.  
```bash
# rpm -qf /usr/bin/find
findutils-4.2.27-5.el5
```

Let's see what are the actual permission of the find program (755).  
```bash
# ls -l /usr/bin/find
-rwxr-xr-x 1 root root 151244 Jul  7  2008 /usr/bin/find
```

Let's change them to 777 with the chmod command  
```bash
# chmod 777 /usr/bin/find
```

Now the program "find" have this permission.
```bash
# ls -l /usr/bin/find
-rwxrwxrwx 1 root root 151244 Jul  7  2008 /usr/bin/find
```

Let's run our command to reset all files included in the package "findutils".
```bash
# rpm --setperms findutils
```

You can see now that the permission of the 'find' are back to their initial state.
```bash
# ls -l /usr/bin/find
-rwxr-xr-x 1 root root 151244 Jul  7  2008 /usr/bin/find
```

Be aware that this command only reset the permission and not the owner/group of the file, as you can see from the example below.

```bash
# ls -l /usr/bin/find
-rwxr-xr-x 1 root root 151244 Jul  7  2008 /usr/bin/find
# chmod 777 /usr/bin/find
# chown jacques.jacques /usr/bin/find
# ls -l /usr/bin/find
-rwxrwxrwx 1 jacques jacques 151244 Jul  7  2008 /usr/bin/find
# rpm --setperms findutils
# ls -l /usr/bin/find
-rwxr-xr-x 1 jacques jacques 151244 Jul  7  2008 /usr/bin/find
```