---
title: 'Creating a Red Hat Cluster: Part 5'
date: 2011-04-16T08:37:44+00:00
layout: post
categories:
  - Cluster
tags:
  - cluster
---
Welcome back to LINternUX, for our last article of this series on how to build a working Red Hat cluster. So far we have a working cluster, but it only move the IP from server to server. In this article, we will put in place everything so that we have an FTP and a web service that will be fully redundant within our cluster. In our previous article, we have create a GFS filesystem under the mount point &#8220;/cadmin&#8221;, this is where we will put our scripts, configuration files and log used for our cluster. The content of the &#8220;/cadmin&#8221; filesystem can be downloaded [here](http://linternux.com/download/cluster/cadmin.tar), it include all the directories structure and scripts used in our cluster articles. After this article, you will have a fully configured cluster, running an ftp and a web service. We will have a lot to do, so let&#8217;s begin.

&nbsp;


## FTP prerequisite

We need to make sure that the ftp server &#8220;vsftpd&#8221; is installed on every server in our cluster. You can check if it is installed by typing the following command ;

```bash
root@gandalf:~# rpm -q vsftpd
vsftpd-2.0.5-16.el5_5.1  
root@gandalf:~#
```

If is not installed, we need to run the following command to instal it on the servers;

```bash
root@bilbo:~# yum install vsftpd
```

We must make sure the vsftpd is not started and doesn't start upon reboot. To do so use the following commands on all servers;

```bash
root@bilbo:~#service vsftpd stop
Shutting down vsftpd:                                      [FAILED]  
root@bilbo:~# chkconfig vsftpd off</span>
```

<!--more-->

## Script to stop/start/status our FTP service

Now we need to create a script for each of our services (ftp and web) that the cluster software will use to stop and start the appropriate service and add it to our cluster configuration. We&#8217;ll put these scripts if our /cadmin GFS filesystem, so it&#8217;s accessible by our 3 servers. We will start by creating the script for the ftp service. The script used by the Red Hat Cluster Suite,  receive one parameter when called by the cluster software. These parameter can be &#8220;stop&#8221;, &#8220;start&#8221; and &#8220;status&#8221;.  You can [download a copy of the script](http://linternux.com/download/cluster/srv_ftp.sh "FTP Service Script") and the [vsftpd configuration file](http://linternux.com/download/cluster/srv_ftp.conf "FTP Service configuration file") if you want. but remember that if you want to use them as is, you must put them in the /cadmin filesystem. The &#8220;[srv_ftp.sh](http://linternux.com/download/cluster/srv_ftp.sh)&#8221; script will go in a subdirectory name &#8220;/cadmin/srv&#8221; and the configuration file &#8220;[srv_ftp.conf](http://linternux.com/download/cluster/srv_ftp.conf)&#8221; must go in &#8220;/cadmin/cfg&#8221; directory. But nothing beat an example, let&#8217;s built the one for our FTP service.

<pre>#! /bin/bash
# ---------------------------------------------------------------------------------
# Script to stop/start and give a status of ftp service in the cluster.
# This script is build to receive 3 parameters.
#    - start :  Executed by cluster to start the application(s) or service(s)
#    - stop  :  Executed by cluster to stop  the application(s) or service(s)
#    - status:  Executed by cluster every 30 seconds to check service status.
# ---------------------------------------------------------------------------------
# Author    : Jacques Duplessis - April 2011
# ---------------------------------------------------------------------------------
#set -x
CDIR="/cadmin"              ; export CDIR       # Root directory for Services
CSVC="$CDIR/srv"            ; export CSVC       # Service Scripts Directory
CCFG="$CDIR/cfg"            ; export CCFG       # Service Config. Directory
INST="srv_ftp"              ; export INST       # Service Instance Name
LOG="$CDIR/log/${INST}.log" ; export LOG        # Service Log file name
HOSTNAME=`hostname -a`      ; export HOSTNAME   # HostName
VSFTPD="/usr/sbin/vsftpd"   ; export VSFTPD     # Service Program name
FCFG="${CCFG}/${INST}.conf" ; export FCFG       # Service Config. file name
RC=0                        ; export RC         # Service Return Code
DASH="---------------------"; export DASH       # Dash Line

# Where the Action Start
# ---------------------------------------------------------------------------------
case "$1" in
  start)  echo -e "\n${DASH}" &gt;&gt; $LOG 2&gt;&1
          echo -e "Starting service $INST on $HOSTNAME at `date`" &gt;&gt; $LOG 2&gt;&1
          echo -e "${VSFTPD} ${FCFG}" &gt;&gt; $LOG 2&gt;&1
          ${VSFTPD} ${FCFG} &gt;&gt; $LOG 2&gt;&1
          RC=$?
          FPID=`ps -ef |grep -v grep |grep ${FCFG} |awk '{ print $2 }'|head -1`
          echo "Service $INST started on $HOSTNAME - PID=${FPID} RC=$RC"&gt;&gt; $LOG
          echo "${DASH}" &gt;&gt; $LOG 2&gt;&1
          ;;
  stop )  echo -e "\n${DASH}" &gt;&gt; $LOG 2&gt;&1
          echo -e "Stopping Service $INST on $HOSTNAME at `date` " &gt;&gt; $LOG
          ps -ef | grep ${FCFG}| grep -v grep &gt;&gt; $LOG 2&gt;&1
          FPID=`ps -ef |grep -v grep |grep ${FCFG} |awk '{ print $2 }'|head -1`
          echo -e "Killing PID ${FPID}" &gt;&gt; $LOG 2&gt;&1
          kill $FPID  &gt;&gt; $LOG 2&gt;&1
          echo -e "Service $INST is stopped ..." &gt;&gt; $LOG 2&gt;&1
          RC=0
          echo "${DASH}" &gt;&gt; $LOG 2&gt;&1
          ;;
  status) COUNT=`ps -ef | grep ${FCFG}| grep -v grep | wc -l`
          FPID=`ps -ef |grep -v grep |grep ${FCFG} |awk '{ print $2 }'|head -1`
          echo -n "`date` Service $INST ($COUNT) on $HOSTNAME"&gt;&gt; $LOG 2&gt;&1
          if [ $COUNT -gt 0 ]
             then echo " - PID=${FPID} - OK"  &gt;&gt; $LOG 2&gt;&1
                  RC=0
             else echo " - NOT RUNNING" &gt;&gt; $LOG 2&gt;&1
                  ps -ef | grep -i ${FCFG} | grep -v grep  &gt;&gt; $LOG 2&gt;&1
                  RC=1
          fi
          ;;
esac
exit $RC</pre>

This script is placed in the directory &#8220;/cadmin/svc&#8221; and name &#8220;svc_ftp.sh&#8221;. Now, let&#8217;s add this script to our ftp service, run the &#8220;system-config-cluster&#8221; command to start the cluster configuration GUI.

&nbsp;

## Add ftp script to our ftp cluster service

Now, let&#8217;s add this script to our ftp service, run the &#8220;system-config-cluster&#8221; command.

> root@gandalf:~#** <span style="color: #0000ff;">system-config-cluster &</span>**

[<img loading="lazy" class="alignleft size-medium wp-image-1853" title="Adding our ftp script to the ftp service" src="http://192.168.1.88/wp-content/uploads/2011/04/Add_ftp_service_01-300x293.png" alt="" width="300" height="292" />](http://192.168.1.88/wp-content/uploads/2011/04/Add_ftp_service_01.png)

&nbsp;

Click on &#8220;Resources&#8221; on the left side and then on  the  &#8220;Create a Resource&#8221;&#8216; button at the bottom right of the screen. This will allow us insert our ftp service script into the cluster configuration.

&nbsp;

[<img loading="lazy" class="alignleft size-medium wp-image-1854" title="Creating a resource for our ftp script" src="http://192.168.1.88/wp-content/uploads/2011/04/Add_ftp_service_02-254x300.png" alt="" width="254" height="300" srcset="http://192.168.1.88/wp-content/uploads/2011/04/Add_ftp_service_02-254x300.png 254w, http://192.168.1.88/wp-content/uploads/2011/04/Add_ftp_service_02.png 373w" sizes="(max-width: 254px) 100vw, 254px" />](http://192.168.1.88/wp-content/uploads/2011/04/Add_ftp_service_02.png)

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

Select &#8220;Script&#8221; from the &#8220;Resource Type&#8221; list and then enter the name of our ressource &#8220;srv_ftp&#8221;  and then specify the name of the script the service will use, with it&#8217;s full path. Here, like I said we decided to place it in our &#8220;/cadmin&#8221; GFS filesystem so it is seen by every node in the cluster.

&nbsp;

[<img loading="lazy" class="alignleft size-medium wp-image-1855" title="Add_ftp_service_03" src="http://192.168.1.88/wp-content/uploads/2011/04/Add_ftp_service_03-300x292.png" alt="" width="300" height="292" srcset="http://192.168.1.88/wp-content/uploads/2011/04/Add_ftp_service_03-300x292.png 300w, http://192.168.1.88/wp-content/uploads/2011/04/Add_ftp_service_03.png 725w" sizes="(max-width: 300px) 100vw, 300px" />](http://192.168.1.88/wp-content/uploads/2011/04/Add_ftp_service_03.png)

&nbsp;

Now we need to edit our &#8220;srv_ftp&#8221; service to add the resource we just created.

&nbsp;

Select the &#8220;srv_ftp&#8221; service at the bottom left of the screen and then press the &#8220;Edit Service Properties&#8221; button.

&nbsp;

[<img loading="lazy" class="alignleft size-medium wp-image-1856" title="Add_ftp_service_04" src="http://192.168.1.88/wp-content/uploads/2011/04/Add_ftp_service_04-300x261.png" alt="" width="300" height="261" srcset="http://192.168.1.88/wp-content/uploads/2011/04/Add_ftp_service_04-300x261.png 300w, http://192.168.1.88/wp-content/uploads/2011/04/Add_ftp_service_04.png 561w" sizes="(max-width: 300px) 100vw, 300px" />](http://192.168.1.88/wp-content/uploads/2011/04/Add_ftp_service_04.png)

&nbsp;

&nbsp;

&nbsp;

Click on the &#8220;Add a Shared Resource to this service&#8221; button. This will bring up the screen below, where we select the &#8220;srv_ftp&#8221; script that we want to add the our service.

&nbsp;

[<img loading="lazy" class="alignleft size-full wp-image-1857" title="Add_ftp_service_05" src="http://192.168.1.88/wp-content/uploads/2011/04/Add_ftp_service_05.png" alt="" width="261" height="260" srcset="http://192.168.1.88/wp-content/uploads/2011/04/Add_ftp_service_05.png 261w, http://192.168.1.88/wp-content/uploads/2011/04/Add_ftp_service_05-150x150.png 150w" sizes="(max-width: 261px) 100vw, 261px" />](http://192.168.1.88/wp-content/uploads/2011/04/Add_ftp_service_05.png)

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

[<img loading="lazy" class="alignleft size-medium wp-image-1858" title="Add_ftp_service_06" src="http://192.168.1.88/wp-content/uploads/2011/04/Add_ftp_service_06-300x260.png" alt="" width="300" height="260" />](http://192.168.1.88/wp-content/uploads/2011/04/Add_ftp_service_06.png)

&nbsp;

After adding our script to the resource, press the &#8220;Close&#8221; button.

We are now ready to push our new configuration to the member of our cluster, press the &#8220;Send to Cluster&#8221; button to do so.

[<img loading="lazy" class="alignleft size-full wp-image-1859" title="Add_ftp_service_07" src="http://192.168.1.88/wp-content/uploads/2011/04/Add_ftp_service_07.png" alt="" width="251" height="96" />](http://192.168.1.88/wp-content/uploads/2011/04/Add_ftp_service_07.png)

&nbsp;

&nbsp;

&nbsp;

&nbsp;

## Web site prerequisite

Make sure that the &#8220;httpd&#8221; and the &#8220;php&#8221; package is installed on every server in our cluster. You can check if it is installed by typing the following command ;

> root@gandalf # **rpm -q httpd php**  
> httpd-2.2.3-45.el5  
> php-5.1.6-27.el5_5.3  
> root@gandalf #
> 
> #

If is not installed, we need to run the following command to instal them on the servers where it&#8217;s not installed ;

> root@bilbo:~# **yum install httpd php  
>** 

We must make sure the &#8220;httpd&#8221; is not started and doesn&#8217;t start upon reboot. To do so use the following commands on all servers;

> root@bilbo:~# **service httpd stop**  
> Shutting down httpd:                                      [FAILED]  
> root@bilbo:~# **chkconfig httpd off**

## Script to stop/start/status our Web service

We have simplify the configuration of our web site to the minimum. This was done intentionnaly, we wanted to demonstrate the cluster functionnaly and not the &#8220;httpd&#8221; possibilites. But our web site will be functionnal and redundant. As with the ftp script, the function of our web server script is very similar. You can [download this script](http://linternux.com/download/cluster/srv_www.sh "Web Service Script") and the [httpd configuration file](http://linternux.com/download/cluster/srv_www.conf "HTTPD configuration file") if you want, but remember that if you want to use them as is, you must put them in the /cadmin filesystem. The &#8220;[srv_www.sh](http://linternux.com/download/cluster/srv_www.sh)&#8221; script will go in a subdirectory name &#8220;/cadmin/srv&#8221; and the configuration file &#8220;[srv_www.conf](http://linternux.com/download/cluster/srv_www.conf)&#8221; must go in &#8220;/cadmin/cfg&#8221; directory.

<pre>#! /bin/bash
# ---------------------------------------------------------------------------------
# Script to stop/start and give a status of our web service in the cluster.
# This script is build to receive 3 parameters.
#    - start :  Executed by cluster to start the application(s) or service(s)
#    - stop  :  Executed by cluster to stop  the application(s) or service(s)
#    - status:  Executed by cluster every 30 seconds to check service status.
# ---------------------------------------------------------------------------------
# Author    : Jacques Duplessis - April 2011
# ---------------------------------------------------------------------------------
#set -x
CDIR="/cadmin"              ; export CDIR       # Root directory for Services
CSVC="$CDIR/srv"            ; export CSVC       # Service Scripts Directory
CCFG="$CDIR/cfg"            ; export CCFG       # Service Config. Directory
INST="srv_www"              ; export INST       # Service Instance Name
LOG="$CDIR/log/${INST}.log" ; export LOG        # Service Log file name
HOSTNAME=`hostname -a`      ; export HOSTNAME   # HostName
HTTPD="/usr/sbin/httpd"     ; export HTTPD      # Service Program name
HCFG="${CCFG}/${INST}.conf" ; export HCFG       # Service Config. file name
RC=0                        ; export RC         # Service Return Code
DASH="---------------------"; export DASH       # Dash Line

# Where the Action Start
# ---------------------------------------------------------------------------------
case "$1" in
 start)  echo -e "\n${DASH}" &gt;&gt; $LOG 2&gt;&1
         echo -e "Starting service $INST on $HOSTNAME at `date`" &gt;&gt; $LOG 2&gt;&1
         echo -e "${HTTPD} ${HCFG}" &gt;&gt; $LOG 2&gt;&1
         ${HTTPD} -f ${HCFG} &gt;&gt; $LOG 2&gt;&1
         RC=$?
         HPID=`cat ${CCFG}/${INST}.pid`
         echo "Service $INST started on $HOSTNAME - PID=${HPID} RC=$RC"&gt;&gt; $LOG
         echo "${DASH}" &gt;&gt; $LOG 2&gt;&1       
         ;;
 stop )  echo -e "\n${DASH}" &gt;&gt; $LOG 2&gt;&1
         echo -e "Stopping Service $INST on $HOSTNAME at `date` " &gt;&gt; $LOG
         HPID=`cat ${CCFG}/${INST}.pid`
         echo -e "Killing PID ${HPID}" &gt;&gt; $LOG 2&gt;&1
         kill $HPID  &gt; /dev/null 2&gt;&1
         echo -e "Service $INST is stopped ..." &gt;&gt; $LOG 2&gt;&1
         RC=0
         echo "${DASH}" &gt;&gt; $LOG 2&gt;&1       
         ;;
 status) COUNT=`ps -ef | grep ${HCFG}| grep -v grep | wc -l`
         HPID=`cat ${CCFG}/${INST}.pid`
         echo -n "`date` Service $INST ($COUNT) on $HOSTNAME"&gt;&gt; $LOG 2&gt;&1
         if [ $COUNT -gt 0 ]
            then echo " - PID=${HPID} - OK"  &gt;&gt; $LOG 2&gt;&1
                 RC=0
            else echo " - NOT RUNNING" &gt;&gt; $LOG 2&gt;&1
                 ps -ef | grep -i ${HCFG} | grep -v grep  &gt;&gt; $LOG 2&gt;&1
                 RC=1
         fi
         ;;
esac
exit $RC</pre>

## Updating our cluster Configuration

To add our web service, please follow the same sequence as we did when we inserted our ftp service into the cluster configuration. You only need to replace &#8220;srv\_ftp.sh&#8221; by &#8220;srv\_www.sh&#8221; and the script path will be the same, we have decide to place our scripts into the directory &#8220;/cadmin/srv&#8221;. Once we have push the new configuration to all servers in the cluster, we should now have a working cluster.The web site define in the configuration have its &#8220;Root Directory&#8221; set to &#8220;/cadmin/www/html&#8221; it contains only one file that will display the name of the it is running on. The will help us testing our cluster configuration.

I you wish to use the cluster configuration, scripts and configuration files we have used in this series of articles, I would encourage you to download the &#8220;[cadmin.tar](http://linternux.com/download/cluster/cadmin.tar "Complete Working Environment")&#8221; file. The file is the actual content of the &#8220;/cadmin&#8221; directory used throught out this article. To use it, download the &#8220;[cadmin.tar](http://linternux.com/download/cluster/cadmin.tar)&#8221; file then copy it to your &#8220;/cadmin&#8221; directory and enter the command &#8220;tar -xvf ./cadmin.tar&#8221;. This will explode the tar file and then you will have the working envirionnment I used in this article.

## Testing our ftp service

So here we are (finally you would say &#8230; hum me too), we have now a fully working cluster. So if we issue the &#8220;clustat&#8221; command this is what we should see.

<pre>root@gollum:/# <strong>clustat</strong>
Cluster Status for our_cluster @ Sat Apr 16 11:37:25 2011
Member Status: Quorate

 Member Name                        ID   Status
 ------ ----                       ---- ------
 hbbilbo.maison.ca                    1 Online, rgmanager
 hbgandalf.maison.ca                  2 Online, rgmanager
 hbgollum.maison.ca                   3 Online, Local, rgmanager

 Service Name                     Owner (Last)            State        
 ------- ----                     ----- ------            -----        
 <strong>service:srv_ftp                  hbgollum.maison.ca      started </strong>     
 service:srv_www                  hbbilbo.maison.ca       started      
root@gollum:/#</pre>

From the information above, we can see that all our cluster member status are online and that the resource manager is running on all of them. The resource manager is important, it is responsable for moving service around when needed. Our service &#8220;srv\_ftp&#8221; is started (running) on the &#8220;hbgollum&#8221; server and &#8220;srv\_www&#8221; is running on the &#8220;hbbilbo&#8221; like we decided at the beginning (remember ?

<pre>root@gollum:/#<strong> ip addr show | grep 192</strong>
    inet 192.168.1.104/24 brd 192.168.1.255 scope global eth0
    inet <strong>192.168.1.204</strong>/24 scope global secondary eth0
root@gollum:/#
root@gollum:/#<strong> ps -ef | grep vsftpd | grep -v grep</strong>
root      7858     1  0 10:05 ?        00:00:00 /usr/sbin/vsftpd /cadmin/cfg/srv_ftp.conf
root@gollum:/#</pre>

The command &#8220;ip addr show | grep 192&#8221; is confirming that the virtual IP is defined on &#8220;hbgollum&#8221; server and if we check if the ftp process is also running, we can see that it is. So let&#8217;s try to do an FTP to our virtual IP that we have name &#8220;ftp.maison.ca&#8221; (192.168.1.204). We will try it from the &#8220;gandalf&#8221; server and we see that it is working.

<pre><strong>root@gandalf:/# ftp ftp.maison.ca</strong>
Connected to ftp.maison.ca.
220 ftp.maison.ca
530 Please login with USER and PASS.
530 Please login with USER and PASS.
KERBEROS_V4 rejected as an authentication type
Name (ftp.maison.ca:root):</pre>

&nbsp;

Now let&#8217;s move the ftp service from &#8220;hbgollum&#8221; to &#8220;hbbilbo&#8221;, to see if the ftp service will continue to work. To move the service we will use the &#8220;clusvcadm&#8221; command, we need to specify the service name we need to relocate (-r) and the server (-m for machine) we wish to move it. You can issue the &#8220;clusvccmd&#8221; command on any of the server within our cluster. So enter the following command to move our service to &#8220;hbbilbo&#8221; ;

<pre>root@gandalf:/# <strong>clusvcadm -r srv_ftp -m hbbilbo</strong>
'hbbilbo' not in membership list
Closest match: 'hbbilbo.maison.ca'
Trying to relocate service:srv_ftp to hbbilbo.maison.ca...Success
service:srv_ftp is now running on hbbilbo.maison.ca
root@gandalf:/#</pre>

Notice that just after pressing [Enter], we got &#8220;&#8216;hbbilbo&#8217; nit in membership list&#8221;, this is because we did not mention the domain name &#8216;maison.ca&#8217;, but it managed to assume that we were refering to &#8220;hbbilbo.maison.ca&#8221;. So our command succeeded, so let&#8217;s see if everything went like it should have.

Fisrt, let&#8217;s execute the clustat command to see in the &#8220;srv_ftp&#8221; service is now running on &#8216;hbbilbo&#8217;.

<pre>root@gandalf:/# <strong>clustat </strong>
Cluster Status for our_cluster @ Sat Apr 16 12:10:01 2011
Member Status: Quorate

 Member Name                      ID   Status
 ------ ----                      ---- ------
 hbbilbo.maison.ca                    1 Online, rgmanager
 hbgandalf.maison.ca                  2 Online, Local, rgmanager
 hbgollum.maison.ca                   3 Online, rgmanager

 Service Name            Owner (Last)            State
 ------- ----            ----- ------            -----
<strong> service:srv_ftp         hbbilbo.maison.ca       started      </strong>
 service:srv_www         hbbilbo.maison.ca       started
root@gandalf:/#</pre>

We can see that our ftp service is now running on &#8220;hbbilbo&#8221;, let&#8217;s see if it reallly is. If we check if the 192.168.1.204 (ftp.maison,ca) is now defined on &#8220;hbbilbo&#8217; we can see that it is. The FTP process is also running now on the server.

<pre><strong>root@bilbo:~# ip addr show | grep 192</strong>
    inet 192.168.1.111/24 brd 192.168.1.255 scope global eth0
    inet 192.168.1.211/24 scope global secondary eth0
    inet 192.168.1.204/24 scope global secondary eth0
root@bilbo:~# <strong>ps -ef | grep vsftpd | grep -v grep</strong>
root      8616     1  0 12:05 ?        00:00:00 /usr/sbin/vsftpd /cadmin/cfg/srv_ftp.conf
root@bilbo:~#</pre>

But what happen on &#8216;hbgollum&#8217;, the IP 192.168.1.204 should not be there anymore and the FTP process should not be running anymore. So that&#8217;s what happen the IP is gone and the ftp process is no longer running. So far so good, the move to &#8216;hbbilbo&#8217; server have worked.

<pre><strong>root@gollum:/etc/profile.d#<strong> ip addr show | grep 192</strong></strong>
    inet 192.168.1.104/24 brd 192.168.1.255 scope global eth0
root@gollum:/etc/profile.d# ps -ef | grep vsftpd | grep -v grep
root@gollum:/etc/profile.d#</pre>

The last test if to try to do an ftp to ftp.maison.ca and see if it respond.

<pre>root@gandalf:/# <strong>ftp ftp.maison.ca</strong>
Connected to ftp.maison.ca.
220 ftp.maison.ca
530 Please login with USER and PASS.
530 Please login with USER and PASS.
KERBEROS_V4 rejected as an authentication type
Name (ftp.maison.ca:root):</pre>

Great evrything worked !! Let&#8217;s move the ftp process back to &#8216;hbgollum&#8217; before testing our web site. Open another terminal window and enter the &#8220;clustat -i 2&#8221; command and see watch the status change from &#8216;started/stopping/starting/started&#8217; while the move is going on. Check your /var/log/message and familiar yourself with the line recorded when the move happen.

<pre>root@gandalf:/# <strong>clusvcadm -r srv_ftp -m hbgollum</strong>
'hbgollum' not in membership list
Closest match: 'hbgollum.maison.ca'
Trying to relocate service:srv_ftp to hbgollum.maison.ca...Success
service:srv_ftp is now running on hbgollum.maison.ca
root@gandalf:/#</pre>

One of the test we should make, is to unplug the network cable or poweroff &#8220;hbgollum&#8221; and see if the service move to the next server in the failover domain (It will). So we have now completed and tested our ftp service. It as been a long road but it woth it, no ?

&nbsp;

&nbsp;

## Testing our web service

You know now, how to move service from server to another, Let&#8217;s do the same test with our web server service. The web site is actually just one simple page. It just display the name of the server that it is running on, this simplify our testing.

If we issue the &#8220;clustat&#8221; command we should have the following picture ;

<pre># <strong>clustat</strong>
Cluster Status for our_cluster @ Sat Apr 16 14:15:37 2011
Member Status: Quorate

Member Name                      ID   Status
------ ----                      ---- ------
hbbilbo.maison.ca                    1 Online, rgmanager
hbgandalf.maison.ca                  2 Online, Local, rgmanager
hbgollum.maison.ca                   3 Online, rgmanager

Service Name            Owner (Last)            State
------- ----            ----- ------            -----
service:srv_ftp         hbgollum.maison.ca      started
<strong>service:srv_www         hbbilbo.maison.ca       started</strong></pre>

Let&#8217;s see if it is working, open your browser type this URL &#8220;http://www.maison.ca&#8221; , you should have a the following ;

<p style="text-align: center;">
  <a href="http://192.168.1.88/wp-content/uploads/2011/04/www_bilbo.png"><img loading="lazy" class="size-medium wp-image-1911 aligncenter" title="www_bilbo" src="http://192.168.1.88/wp-content/uploads/2011/04/www_bilbo-300x92.png" alt="" width="300" height="92" /></a>
</p>

<p style="text-align: left;">
  Now, let&#8217;s move the wev site to &#8220;gandalf&#8221; server, type the following command ;
</p>

<pre>root@gollum:/cadmin/cfg# <strong>clusvcadm -r srv_www -m hbgandalf</strong>
'hbgandalf' not in membership list
Closest match: 'hbgandalf.maison.ca'
Trying to relocate service:srv_www to hbgandalf.maison.ca...Success
service:srv_www is now running on hbgandalf.maison.ca
root@gollum:/cadmin/cfg#
root@gollum:/cadmin/cfg#<strong> clustat</strong>
Cluster Status for our_cluster @ Sat Apr 16 14:27:14 2011
Member Status: Quorate

 Member Name                         ID   Status
 ------ ----                         ---- ------
 hbbilbo.maison.ca                       1 Online, Local, rgmanager
 hbgandalf.maison.ca                     2 Online, rgmanager
 hbgollum.maison.ca                      3 Online, rgmanager

 Service Name               Owner (Last)               State
 ------- ----               ----- ------               -----
 service:srv_ftp            hbgollum.maison.ca         started
<strong> service:srv_www            hbgandalf.maison.ca        started </strong></pre>

<p style="text-align: left;">
  <a href="http://192.168.1.88/wp-content/uploads/2011/04/www_gandalf.png"><img loading="lazy" class="aligncenter size-medium wp-image-1913" title="www_gandalf" src="http://192.168.1.88/wp-content/uploads/2011/04/www_gandalf-300x92.png" alt="" width="300" height="92" /></a>
</p>

<p style="text-align: left;">
  &nbsp;
</p>

We can see that the web site is now running on &#8220;gandalf&#8221; server.

&nbsp;

&nbsp;

## Disabling and Enabling Services

There may come a time, when you need to stop a service completely. We will demonstrate how to acheive that, first let&#8217;s display the status of our cluster

<pre>root@bilbo:~# <strong>clustat</strong>
Cluster Status for our_cluster @ Sat Apr 16 14:39:48 2011
Member Status: Quorate

 Member Name                                  ID   Status
 ------ ----                                  ---- ------
 hbbilbo.maison.ca                                1 Online, Local, rgmanager
 hbgandalf.maison.ca                              2 Online, rgmanager
 hbgollum.maison.ca                               3 Online, rgmanager

 Service Name                        Owner (Last)                        State
 ------- ----                        ----- ------                        -----
 service:srv_ftp                     hbgollum.maison.ca                  started
<strong> service:srv_www                     hbgandalf.maison.ca                 started  </strong></pre>

We are going to disable the &#8220;srv_www&#8221; service, to do so enter the following command ;

<pre>root@bilbo:~# <strong>clusvcadm -d srv_www </strong>
Local machine disabling service:srv_www...Success</pre>

The clustat command show us that the service is now disable.

<pre>root@bilbo:~# <strong>clustat</strong>
Cluster Status for our_cluster @ Sat Apr 16 14:40:04 2011
Member Status: Quorate

 Member Name                                  ID   Status
 ------ ----                                  ---- ------
 hbbilbo.maison.ca                                1 Online, Local, rgmanager
 hbgandalf.maison.ca                              2 Online, rgmanager
 hbgollum.maison.ca                               3 Online, rgmanager

 Service Name                        Owner (Last)                        State
 ------- ----                        ----- ------                        -----
 service:srv_ftp                     hbgollum.maison.ca                  started
 service:srv_www                     (hbgandalf.maison.ca)               disabled</pre>

We will now enable the service, but this time we will enable it on another server than &#8220;hbgandalf&#8221;. This command enable the &#8220;srv_www&#8221; service on the server &#8220;hbbilbo&#8221;.

<pre>root@bilbo:~# <strong>clusvcadm -e srv_www -m hbbilbo</strong>
'hbbilbo' not in membership list
Closest match: 'hbbilbo.maison.ca'
Member hbbilbo.maison.ca trying to enable service:srv_www...Success
service:srv_www is now running on hbbilbo.maison.ca
root@bilbo:~#</pre>

We can see the it is now running on &#8220;hbbilbo&#8221;.

<pre>root@bilbo:~# <strong>clustat</strong>
Cluster Status for our_cluster @ Sat Apr 16 14:47:36 2011
Member Status: Quorate

 Member Name                                  ID   Status
 ------ ----                                  ---- ------
 hbbilbo.maison.ca                                1 Online, Local, rgmanager
 hbgandalf.maison.ca                              2 Online, rgmanager
 hbgollum.maison.ca                               3 Online, rgmanager

 Service Name                        Owner (Last)                        State
 ------- ----                        ----- ------                        -----
 service:srv_ftp                     hbgollum.maison.ca                  started
<strong> service:srv_www                     hbbilbo.maison.ca                   started  </strong>
root@bilbo:~#</pre>

&nbsp;

&nbsp;

This conclude our implementation of a small cluster. It was intended just to show every one how the Red Hat Cluster Suite actually work and to give a brief overview how it work. We will now move on to other interesting topic. Don&#8217;t know what it will be, but I can assure that it should fit into one article, So I hope you appreciate it and hope to see you soon.

&nbsp;

<a title="Part 1 - Creating a RedHat/CentOS Cluster" href="http://linternux.com/2010/11/creating-cluster-part-1/" target="_blank" rel="noopener">Part 1 &#8211; Creating a Linux ReadHat/CentOS cluster</a>

<a href="http://linternux.com/2011/01/creating-a-redhatcentos-cluster-part-2/" target="_blank" rel="noopener">Part 2 &#8211; Creating a Linux ReadHat/CentOS cluster</a>

<a title="Part 3 of Creating a RedHat/CentOS cluster" href="http://linternux.com/2011/03/creating-a-red-hatcentos-cluster-part-3/" target="_blank" rel="noopener">Part 3 &#8211; Creating a Linux ReadHat/CentOS cluster</a>

<a title="Part 4 of Creating a RedHat/CentOS cluster" href="http://linternux.com/2011/04/creating-a-redhat-cluster-part-4/" target="_blank" rel="noopener">Part 4 &#8211; Creating a Linux ReadHat/CentOS cluster</a>

<a title="Part 5 of Creating a RedHat/CentOS cluster" href="http://linternux.com/2011/04/creating-a-redhat-cluster-part-5/" target="_blank" rel="noopener">Part 5 &#8211; Creating a Linux ReadHat/CentOS cluster</a>

&nbsp;