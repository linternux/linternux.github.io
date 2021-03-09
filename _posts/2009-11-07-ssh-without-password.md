---
id: 1074
title: Using SSH and SCP without entering password
date: 2009-11-07T08:58:59+00:00
author: jacques
layout: post
guid: http://linternux.com/?p=1074
permalink: /index.php/2009/11/07/ssh-without-password/
aktt_notify_twitter:
  - 'yes'
categories:
  - Network
tags:
  - ssh
---
When administrating a lot of Unix servers, there are some situation when you need to run a script from one server to another without entering a password. For example, let&#8217;s say that you need to take a cold backup of a Oracle database, but before starting it, you need to stop the application running on another server. In your Oracle backup script, you could &#8220;ssh&#8221; to application server and run a script that would stop the application before starting the backup.  But to do that with a script, you need a way to log on the application server without having to enter a password.

In this article, we will demonstrate how to configure SSH in such a way that it will allow you to log from one server to another, without having to enter a password.  Some environment are using the [OpenSSH](http://www.openssh.com/ "OpenSSH Home page") version on their Linux servers and the commercial [Tectia SSH](http://www.ssh.com/ "Tectia SSH Home Page") on the [AIX](http://www-03.ibm.com/systems/power/software/aix/index.html "AIX Home Page at IBM") servers. OpenSSH and Tectia SSH don&#8217;t have the same keys format and depending on the version you are running,  making an automated connection between these two version can become tricky. In our examples, we will demonstrate the setup require, so that user &#8220;robert&#8221; is able to log from server1 to server2 without having to enter a password in a mixed environment of OpenSSH and Tectia SSH.

#### OpenSSH server configuration (/etc/ssh/sshd_config)

If you are using OpenSSH and you have secure your ssh environnent, chance are that you disable direct &#8220;root&#8221; access to your server with the line &#8220;PermitRootLogin no&#8221; in your ssh daemon configuration file. If you change that line with &#8220;PermitRootLogin without-password&#8221;, then direct login to &#8220;root&#8221; would still be refuse.  But, if you have configure your server to accept public key identification (PubkeyAuthentication yes) and that the proper setup is done, you should be able to log on the server with no password.  Below is the Openssh configuration file that I use for all the examples below.**  
** 

<pre>Port                     22
Protocol                 2
SyslogFacility           AUTH
SyslogFacility           AUTHPRIV
LoginGraceTime           120
PermitRootLogin          without-password
PubkeyAuthentication    yes
HostbasedAuthentication  no
PasswordAuthentication  yes
RhostsRSAAuthentication  no
IgnoreRhosts             yes
StrictModes              yes
UsePrivilegeSeparation  yes
AllowTcpForwarding       no
X11Forwarding            yes
Subsystem sftp           /usr/libexec/openssh/sftp-server</pre>

The OpenSSH version used for all the examples below is ;

<pre># ssh -V
OpenSSH_4.3p2, OpenSSL 0.9.8e-fips-rhel5 01 Jul 2008</pre>

 ****

 ****

 ****

<span style="font-size: medium;"><strong> </strong></span>

<span style="font-size: medium;"><strong> </strong></span>

#### Tectia SSH server configuration (/etc/ssh2/ssh-server-config.xml)

The only action needed to permit public key authentication for users is to list &#8216;publickey&#8217; as an allowed authentication method in the _ssh-server-config.xml_ file:

<pre>&lt;authentication-methods&gt;
  &lt;authentication action="allow"&gt;
    &lt;auth-publickey /&gt;
    ...
  &lt;/authentication&gt;
&lt;/authentication-methods&gt;</pre>

Other authentication methods can also be allowed. Place the least interactive method first.

For all the Tectia SSH examples below we used the following version ;

<pre># sshg3 -V
sshg3.bin: SSH Tectia Client 6.1.3 on powerpc-ibm-aix5.1.0.0
Build: 59
Product: SSH Tectia Client</pre>

 ****

## ** <span style="color: #0000ff;"></span>**

## ** <span style="color: #0000ff;"></span>**

## **<span style="color: #0000ff;">Automating SSH connection from OpenSSH to OpenSSH</span>**

[<img loading="lazy" class="size-medium wp-image-1134 alignnone" title="openssh_2_openssh" src="http://192.168.1.88/wp-content/uploads/2009/11/openssh_2_openssh1-300x212.PNG" alt="openssh_2_openssh" width="300" height="212" />](http://192.168.1.88/wp-content/uploads/2009/11/openssh_2_openssh1.png)

 ****

<!--more-->This example demonstrate how to setup public key authentication so that user &#8220;robert&#8221; can log from server1 (OpenSSH) to server2 (OpenSSH), without having to enter a password.

**1) Generate the private and public key for user “robert” on server1.**

<pre>robert@server1:~$ <strong><span style="color: #0000ff;">ssh-keygen</span>
</strong>Generating public/private rsa key pair.
Enter file in which to save the key (/home/robert/.ssh/id_rsa):
Created directory '/home/robert/.ssh'.
Enter passphrase (empty for no passphrase): <span style="color: #0000ff;"><strong>&lt;CR&gt;</strong></span>
Enter same passphrase again: <strong><span style="color: #0000ff;">&lt;CR&gt;</span></strong>
Your identification has been saved in /home/robert/.ssh/id_rsa.
Your public key has been saved in /home/robert/.ssh/id_rsa.pub.
The key fingerprint is:
6f:17:ac:ac:b6:9c:13:90:57:aa:ee:1c:b1:e0:93:e2
<a href="mailto:robert@server1robert@server1:~$">robert@server1

robert@server1:~$</a> <strong><span style="color: #0000ff;">cd .ssh</span></strong>
robert@server1:~/.ssh$ <strong><span style="color: #0000ff;">ls -l</span></strong>
total 3
-rw------- 1 robert robert 1675 Nov 22 08:19 <span style="color: #0000ff;">id_rsa</span>
-rw-r--r-- 1 robert robert  404 Nov 22 08:19 <span style="color: #0000ff;">id_rsa.pub</span>
robert@server1:~/.ssh$</pre>

 ****

 ****

**2) Copy the public key file to a more descriptive name.**

<pre>robert@server1:~/.ssh$<strong><span style="color: #0000ff;"> cp id_rsa.pub server1_rsa.pub</span></strong>
robert@server1:~/.ssh$ <strong><span style="color: #0000ff;">ls -l</span></strong>
total 4
-rw------- 1 robert robert 1675 Nov 22 08:19 id_rsa
-rw-r--r-- 1 robert robert  404 Nov 22 08:19 id_rsa.pub
-rw-r--r-- 1 robert robert  404 Nov 22 08:24 <strong><span style="color: #0000ff;">server1_rsa.pub</span></strong>
robert@server1:~/.ssh$</pre>

 ****

 ****

**3) Create the .ssh directory on the remote server and copy the public key from server1 onto server2.**

<pre>robert@server2:~$<strong> <span style="color: #0000ff;">pwd</span></strong>
<strong>/home/robert</strong>
robert@server2:~$ <strong><span style="color: #0000ff;">mkdir .ssh</span></strong>
robert@server2:~$ <strong><span style="color: #0000ff;">chmod 700 .ssh</span></strong>
robert@server2:~$ <strong><span style="color: #0000ff;">cd .ssh</span></strong>
robert@server2:~/.ssh$ <strong><span style="color: #0000ff;">scp robert@server1:/home/robert/.ssh/server1_rsa.pub .</span></strong>
The authenticity of host 'server1 (192.168.1.101)' can't be established.
RSA key fingerprint is b4:8d:56:71:c9:7e:97:ba:79:87:95:0d:8a:29:fc:9a.
Are you sure you want to continue connecting (yes/no)? <strong><span style="color: #0000ff;">yes</span></strong>
Warning: Permanently added 'server1,192.168.1.101' (RSA) to the list of known hosts.
robert@server1's password: <span style="color: #0000ff;"><strong>*********</strong></span>
server1_rsa.pub                                    100%  404     0.4KB/s   00:00</pre>

 ****

 ****

**4) Add &#8220;robert&#8221; public key from server1 to the authorized_keys file on server2**

<pre>robert@server2:~/.ssh$ <strong><span style="color: #0000ff;">ls -l</span></strong>
total 2
-rw-r--r-- 1 robert robert 401 Nov 22 08:29 known_hosts
-rw-r--r-- 1 robert robert 404 Nov 22 08:31 <span style="color: #0000ff;"><strong>server1_rsa.pub</strong></span>
robert@server2:~/.ssh$
robert@server2:~/.ssh$ <strong><span style="color: #0000ff;">cat server1_rsa.pub &gt;&gt; authorized_keys</span></strong>
robert@server2:~/.ssh$ <strong><span style="color: #0000ff;">chmod 644 authorized_keys</span></strong></pre>

 ****

 ****

**5) Test our automated ssh connection from server1 to server2**

The first time you will need to accept connection. After that first connection, there will be no confirmation needed.

<pre>robert@server1:~/.ssh$ <strong><span style="color: #0000ff;">ssh server2</span></strong>
The authenticity of host 'server2 (192.168.1.133)' can't be established.
RSA key fingerprint is 28:cc:97:fb:ec:79:94:7f:bf:ef:82:bd:d2:c4:41:d2.
Are you sure you want to continue connecting (yes/no)? <strong><span style="color: #0000ff;">yes</span></strong>
Warning: Permanently added 'server2,192.168.1.133' (RSA) to the list of known hosts.
robert@server2:~$

<span style="color: #0000ff;"><strong>robert@server1</strong></span>:~/.ssh$ <strong><span style="color: #0000ff;">ssh server2</span></strong>
Last login: Sun Nov 22 08:36:41 2009 from server1.maison.ca
<strong><span style="color: #0000ff;">robert@server2</span></strong>:~$</pre>

## ** <span style="color: #0000ff;"></span>**

## ** <span style="color: #0000ff;"></span>**

## **<span style="color: #0000ff;">Automating SSH connection from OpenSSH to Tectia SSH</span>**

[<img loading="lazy" class="alignnone size-medium wp-image-1137" title="openssh2ssh2" src="http://192.168.1.88/wp-content/uploads/2009/11/openssh2ssh2-300x229.PNG" alt="openssh2ssh2" width="300" height="229" />](http://192.168.1.88/wp-content/uploads/2009/11/openssh2ssh2.png)

This example demonstrate how to setup public key authentication so that user &#8220;robert&#8221; can log from server1 (OpenSSH) to server2 (Tectia SSH), without having to enter a password.

 ****

 ****

**1) Generate &#8220;robert&#8221; private and public keys on the local server (server1).**

<pre>robert@server1:~$ <strong><span style="color: #0000ff;">ssh-keygen</span></strong>
Generating public/private rsa key pair.
Enter file in which to save the key (/home/robert/.ssh/id_rsa):
Created directory '/home/robert/.ssh'.
Enter passphrase (empty for no passphrase): <strong><span style="color: #0000ff;">&lt;CR&gt;</span></strong>
Enter same passphrase again: <strong><span style="color: #0000ff;">&lt;CR&gt;</span></strong>
Your identification has been saved in /home/robert/.ssh/id_rsa.
Your public key has been saved in /home/robert/.ssh/id_rsa.pub.
The key fingerprint is:
6f:17:ac:ac:b6:9c:13:90:57:aa:ee:1c:b1:e0:93:e2
<a href="mailto:robert@server1robert@server1:~$">robert@server1:~$</a>

<a href="mailto:robert@server1:~$">robert@server1:~$</a> <strong><span style="color: #0000ff;">cd .ssh</span></strong>
robert@server1:~/.ssh$ <strong><span style="color: #0000ff;">ls -l</span></strong>
total 3
-rw------- 1 robert robert 1675 Nov 22 08:19 <span style="color: #0000ff;"><strong>id_rsa</strong></span>
-rw-r--r-- 1 robert robert  404 Nov 22 08:19 <span style="color: #0000ff;"><strong>id_rsa.pub</strong></span>
robert@server1:~/.ssh$</pre>

 ****

 ****

**2) Copy the public key to a more descriptive name and convert to Tectia SSH format**

<pre><a href="mailto:robert@server1:~/.ssh$">robert@server1:~/.ssh$</a> <strong><span style="color: #0000ff;">cp id_rsa.pub server1_rsa.pub</span></strong>
robert@server1:~/.ssh$ <strong><span style="color: #0000ff;">ssh-keygen -e -f server1_rsa.pub &gt; server1_ssh2.pub</span></strong>
robert@server1:~/.ssh$ ls -l
total 6
-rw------- 1 robert robert 1675 Nov 22 08:19 id_rsa
-rw-r--r-- 1 robert robert  404 Nov 22 08:19 id_rsa.pub
-rw-r--r-- 1 robert robert  401 Nov 22 08:36 known_hosts
-rw-r--r-- 1 robert robert  404 Nov 22 08:24 server1_rsa.pub
-rw-rw-r-- 1 robert robert  514 Nov 22 10:15 <strong><span style="color: #0000ff;">server1_ssh2.pub</span></strong>
robert@server1:~/.ssh$</pre>

 ****

 ****

**3) From the remote server (server2) get “robert” Tectia SSH public key from server1.**

<pre><a href="mailto:robert@server2:~/$"><span style="color: #000000;">robert@server2:~/$</span></a> <strong><span style="color: #0000ff;">ls -al</span></strong>
total 4
drwxr-xr-x   2 robert   staff           512 Nov 22 08:59 .
drwxr-xr-x   7 bin      bin             512 Nov 22 08:58 ..
-rwxr-----   1 robert   staff           254 Nov 22 08:58 .profile
-rw-------   1 robert   staff           110 Nov 22 10:08 .sh_history

robert@server2:~/$ <strong><span style="color: #0000ff;">mkdir .ssh2</span></strong>
robert@server2:~/$ <span style="color: #0000ff;"><strong>chmod 700 .ssh2</strong></span>
robert@server2:~/$ <strong><span style="color: #0000ff;">cd .ssh2</span></strong>
robert@server2:~/$ <strong><span style="color: #0000ff;">scpg3 robert@server1:/home/robert/.ssh/server1_ssh2.pub .</span></strong> (replace scpg3 by scp if using V4)
robert@server1 password: <span style="color: #0000ff;"><strong>********</strong></span>
server1_rsa_ssh2.pub                        |   514B |   3.7kiB/s | TOC: 00:00:00 | 100%
robert@server2:~/$</pre>

 ****

 ****

**4) Add &#8220;robert&#8221; public key on server1 to the authorization file on server2.**

 ****

<pre><a href="mailto:robert@server2:~/$">robert@server2:~/$</a> <span style="color: #0000ff;"><strong>echo "Key server1_ssh2.pub" &gt;&gt; ~/.ssh2/authorization</strong></span></pre>

 ****

 ****

 ****

**5) Test our automated ssh connection from server1 to server2**

<pre>robert@server1:~/.ssh$ <strong><span style="color: #0000ff;">ssh robert@server2</span></strong>
The authenticity of host 'server2 (192.168.1.130)' can't be established.
RSA key fingerprint is cb:98:02:be:4c:0d:80:8e:bc:20:cf:45:03:fc:70:54.
Are you sure you want to continue connecting (yes/no)? <span style="color: #0000ff;"><strong>yes</strong></span>
Warning: Permanently added 'server2,192.168.1.130' (RSA) to the list of known hosts.
<span style="color: #0000ff;"><strong>robert@server2</strong></span>:~/.ssh$ 

robert@server1:~/.ssh$ <span style="color: #0000ff;"><strong>ssh robert@server2 date</strong></span>
Sun Nov 22 10:16:06 EST 2009
robert@server1:~/.ssh$</pre>

## ** <span style="color: #0000ff;"></span>**

## ** <span style="color: #0000ff;"></span>**

## **<span style="color: #0000ff;">Automating SSH connection from Tectia SSH to OpenSSH</span>**

[<img loading="lazy" class="alignnone size-medium wp-image-1139" title="ssh2_2_openssh" src="http://192.168.1.88/wp-content/uploads/2009/11/ssh2_2_openssh-300x223.PNG" alt="ssh2_2_openssh" width="300" height="223" />](http://192.168.1.88/wp-content/uploads/2009/11/ssh2_2_openssh.png)

This example demonstrate how to setup public key authentication so that user &#8220;robert&#8221; can log from server1 (Tectia SSH) to server2 (OpenSSH), without having to enter a password.

 ****

 ****

**1) Generate  Tectia SSH private and public keys for user “robert” on server1.**

 ****

<pre><a href="mailto:robert@server1:~/$">robert@server1:~/</a><a href="mailto:robert@server1:~/$">$</a> <span style="color: #0000ff;"><strong>ssh-keygen-g3</strong></span> (use ssh-key if using V4)
Generating 2048-bit dsa key pair
153 oOOo.oOo.oOo
Key generated.
2048-bit dsa, robert@server1, Sun Nov 22 2009 19:21:21 -0500
Passphrase :<strong><span style="color: #0000ff;"> &lt;CR&gt;</span></strong>
Again      :<span style="color: #0000ff;"><strong> &lt;CR&gt;</strong></span>
Key is stored with NULL passphrase.
(You can ignore the following warning if you are generating hostkeys.)
This is not recommended. Don't do this unless you know what you're doing.
If file system protections fail (someone can access the keyfile),
or if the super-user is malicious, your key can be used without the deciphering effort.
Private key saved to /home/robert/.ssh2/id_dsa_2048_a
Public key saved to /home/robert/.ssh2/id_dsa_2048_a.pub
<a href="mailto:robert@server1:~/$">robert@server1:~/</a>$
</pre>

 ****

**1a) Identify the private key name in the identification file (_Not needed for version 5 and above_)**

<pre>robert@server1:~/ $ <strong>echo "IdKey id_dsa_2048_a</strong><strong>" &gt;&gt; identification</strong></pre>

**2) Copy the public key to a more descriptive name.**

<pre><a href="mailto:robert@server1:~/$">robert@server1:~/</a>$<strong><span style="color: #0000ff;"> pwd</span></strong>
/home/robert
<a href="mailto:robert@server1:~/$">robert@server1:~/</a>$ <strong><span style="color: #0000ff;">cd .ssh2</span></strong>
<a href="mailto:robert@server1:~/$">robert@server1:~/</a>$ <span style="color: #0000ff;"><strong>ls -l</strong></span>
total 11
-rw-------   1 robert   staff          1389 Nov 22 19:25 id_dsa_2048_a
-rw-r--r--   1 robert   staff          1257 Nov 22 19:25 <strong><span style="color: #0000ff;">id_dsa_2048_a.pub</span></strong>
<a href="mailto:robert@server1:~/$">robert@server1:~/</a>$ <strong><span style="color: #0000ff;">cp id_dsa_2048_a.pub server1_ssh2.pub</span></strong></pre>

 ****

 ****

 ****

**3) From the remote server (server2) get “robert” Tectia SSH public key from server1.**

<pre>robert@server2:~$ <strong><span style="color: #0000ff;">pwd</span></strong>
/home/robert
robert@server2:~$ <strong><span style="color: #0000ff;">mkdir .ssh</span></strong>
robert@server2:~$ <strong><span style="color: #0000ff;">chmod 700 .ssh</span></strong>
robert@server2:~$ <strong><span style="color: #0000ff;">cd .ssh</span></strong>
robert@server2:~/.ssh$ <strong><span style="color: #0000ff;">scp robert@server1:/home/robert/.ssh2/server1_ssh2.pub .</span></strong>
The authenticity of host 'server1' (192.168.1.130)' can't be established.
RSA key fingerprint is cb:98:02:be:4c:0d:80:8e:bc:20:cf:45:03:fc:70:54.
Are you sure you want to continue connecting (yes/no)? <strong><span style="color: #0000ff;">yes</span></strong>
Warning: Permanently added 'server1,192.168.1.130' (RSA) to the list of known hosts.
Password Authentication:
robert's password: <strong><span style="color: #0000ff;">********</span></strong>

server1_ssh2.pub                                            100% 1257     1.2KB/s   00:00</pre>

 ****

 ****

**4) Convert Tectia SSH key to OpenSSH format & add it to the authorization file.**

<pre>robert@server2:~/.ssh$ <strong><span style="color: #0000ff;">ssh-keygen -i -f server1_ssh2.pub &gt; server1_ssh.pub</span></strong>
robert@server2:~/.ssh$ <strong><span style="color: #0000ff;">cat server1_ssh.pub &gt;&gt; authorized_keys</span></strong>
robert@server2:~/.ssh$ <strong><span style="color: #0000ff;">chmod 644 authorized_keys</span></strong>
<span style="color: #0000ff;"><strong> </strong></span></pre>

**5) Test &#8220;robert&#8221; automated ssh connection from server1 to server2**

<pre><a href="mailto:robert@server1:~/$">robert@server1:~/$</a> <strong><span style="color: #0000ff;">sshg3 robert@server2</span></strong>
Please select how you want to proceed.
cancel) Cancel the connection.
once) Proceed with the connection but do not save the key.
save) Proceed with the connection and save the key for future use.
Please select one (cancel, once, save): <strong><span style="color: #0000ff;">save</span></strong>
Authentication successful.
Last login: Sun Nov 22 08:38:03 2009 from server1.maison.ca
robert@server2:~$

<a href="mailto:robert@server1:~/$">robert@server1:~/</a>$ <strong><span style="color: #0000ff;">sshg3 robert@server1 date</span></strong>
Authentication successful.
Sun Nov 22 19:56:04 EST 2009
<a href="mailto:robert@server1:~/$">robert@server1:~/</a>$</pre>

## ** <span style="color: #0000ff;"></span>**

## ** <span style="color: #0000ff;"></span>**

## **<span style="color: #0000ff;">Automating SSH connection from Tectia SSH to Tectia SSH</span>**

[<img loading="lazy" class="alignnone size-medium wp-image-1140" title="ssh2_2_ssh2" src="http://192.168.1.88/wp-content/uploads/2009/11/ssh2_2_ssh2-300x216.PNG" alt="ssh2_2_ssh2" width="300" height="216" />](http://192.168.1.88/wp-content/uploads/2009/11/ssh2_2_ssh2.png)

This example demonstrate the step needed so that user &#8220;robert&#8221; can log from server1 (Tectia SSH) to server2 who is also running Tectia SSH, without having to enter a password.

 ****

**1) Generate &#8220;robert&#8221; Tectia SSH private and public keys on server1.**

<pre><a href="mailto:robert@server1:~/$">robert@server1:~/</a><a href="mailto:robert@server1:~/$">$</a> <strong><span style="color: #0000ff;">ssh-keygen-g3</span></strong> (ssh-keygen for version 4)
Generating 2048-bit dsa key pair
153 oOOo.oOo.oOo
Key generated.
2048-bit dsa, <a href="mailto:robert@server1">robert@server1</a>, Sun Nov 22 2009 19:21:21 -0500
Passphrase : <strong><span style="color: #0000ff;">&lt;CR&gt;</span></strong>
Again      : <strong><span style="color: #0000ff;">&lt;CR&gt;</span></strong>
Key is stored with NULL passphrase.
(You can ignore the following warning if you are generating hostkeys.)
This is not recommended. Don't do this unless you know what you're doing.
If file system protections fail (someone can access the keyfile),
or if the super-user is malicious, your key can be used without the deciphering effort.
Private key saved to /home/robert/.ssh2/id_dsa_2048_a
Public key saved to /home/robert/.ssh2/id_dsa_2048_a.pub
<a href="mailto:robert@server1:~/$">robert@server1:~/</a><a href="mailto:robert@server1:~/$">$</a></pre>

 ****

 ****

**2) Copy the public key to a more descriptive name.**

 ****

<pre>robert@server1:~$ <span style="color: #0000ff;"><strong>pwd</strong></span>
/home/robert
robert@server1:~ $ c<strong><span style="color: #0000ff;">d .ssh2</span></strong>
robert@server1:~ $ <span style="color: #0000ff;"><strong>ls -l</strong>
</span>total 11
-rw-------   1 robert   staff          1389 Nov 22 19:25 id_dsa_2048_a
-rw-r--r--   1 robert   staff          1257 Nov 22 19:25 <strong><span style="color: #0000ff;">id_dsa_2048_a.pub</span></strong>
robert@server1:~ $
robert@server1:~ $ <strong><span style="color: #0000ff;">cp id_dsa_2048_a.pub server1_ssh2.pub</span></strong></pre>

**3) Identify the private key name in the identification file (_<span style="color: #800000;">Not needed for version 5 and above</span>_)**

<pre>robert@server1:~/ $ <strong><span style="color: #0000ff;">echo "IdKey id_dsa_2048_a</span></strong><strong><span style="color: #0000ff;">" &gt;&gt; identification</span></strong></pre>

**4) Get “robert” Tectia SSH public key from server1 onto server2.**

<pre><a href="mailto:robert@server2:~/">robert@server2:~/</a><a href="mailto:robert@server1:~/$">$</a> <strong><span style="color: #0000ff;">scpg3 </span></strong><a href="mailto:robert@server1:/home/robert/.ssh2/server1_ssh2.pub"><strong><span style="color: #0000ff;">robert@server1:/home/robert/.ssh2/server1_ssh2.pub</span></strong></a><strong><span style="color: #0000ff;"> .</span></strong> (use scp in V4)
robert@server1s password: <strong><span style="color: #0000ff;">********</span></strong>
server1_ssh2.pub                                   |   514B |   3.7kiB/s | TOC: 00:00:00 | 100%
<a href="mailto:robert@server2:~/">robert@server2:~/</a><a href="mailto:robert@server1:~/$">$</a></pre>

 ****

 ****

 ****

**5) Add &#8220;robert&#8221; public key from  server1 to the authorization file on server2.**

 ****

<pre><a href="mailto:robert@server2:~/$">robert@server2:~/$</a> <span style="color: #0000ff;"><strong>echo "Key server1_ssh2.pub" &gt;&gt; ~/.ssh2/authorization</strong></span></pre>

 ****

 ****

 ****

**6) Test our automated ssh connection from server1 to server2**

<pre><a href="mailto:robert@server1:~/$">robert@server1:~/</a><a href="mailto:robert@server1:~/$">$</a> <strong><span style="color: #0000ff;">sshg3 </span></strong><a href="mailto:robert@server2"><strong><span style="color: #0000ff;">robert@server2</span></strong></a>
Please select how you want to proceed.
cancel) Cancel the connection.
once) Proceed with the connection but do not save the key.
save) Proceed with the connection and save the key for future use.
Please select one (cancel, once, save): <strong><span style="color: #0000ff;">save</span></strong>
Authentication successful.
Last login: Sun Nov 22 08:38:03 2009 from server1.maison.ca
<a href="mailto:robert@server2:~$"><strong><span style="color: #0000ff;">robert@server2:~$</span></strong></a>

<a href="mailto:robert@server1:~/$">robert@server1:~/</a><a href="mailto:robert@server1:~/$">$</a> <strong><span style="color: #0000ff;">sshg3 robert@server1 date</span></strong>
Authentication successful.
Sun Nov 22 19:56:04 EST 2009
<a href="mailto:robert@server1:~/$">robert@server1:~/</a><a href="mailto:robert@server1:~/$">$</a></pre>

Hope this article will be usefull for many of us and I hope that you will come back to it when ever you need it.

See you soon.