---
layout: post
title: sadm_daily_report.sh
date: 2008-02-11 09:28:10
date_updated: 2009-02-12 
tags: manpage script 
categories: sadm_scripts linux
---

This script produce web reports of the last 24 hrs activities and email them to the sysadmin. It run automatically early every morning from the SADMIN server crontab (/etc/cron.d/sadm_server), but you can also be run it manually when desired. It can only be run on the SADMIN server.

{% highlight bash %}
$ sudo $SADMIN/bin/sadm_daily_report.sh [-v] [-h] [-b] [-r] [-s] [-d 0-9]  
{% endhighlight %}


## DESCRIPTION
The script produce three reports, each of them is sent to the email(s) defined by the field '*SADM_MAIL_ADDR*' defined in the SADMIN configuration file ($SADMIN/cfg/sadmin.cfg). It can only be run on the SADMIN server (Not on a SADMIN client). The reports are also accessible via the web interface.

If the package '[wkhtmltopdf](https://wkhtmltopdf.org/)' is present on the SADMIN server (It should have been installed by the setup process), a PDF version of each report is also attached to the email.

If one of the three report is not desired, you can use the '-r', '-b' or the '-s' command line switch to disable the desired report (email). Note that only the actives systems will appear on these reports.  

### The scripts report:
![Daily Script Report Example](/assets/img/man/sadm_daily_report/sadm_daily_report_script.png)
  - The running scripts are listed first, then the scripts that terminated with an error and finally all the scripts grouped by system. So it give you view of the statistics of each script (start time, end time, elapse time,...) of each systems sorted and grouped by name. When something look different than the normal, like the alert group used is different than the default group or the last execution date of a script is more than thirty days, it will be shown with a yellow background. 
  - You can also view the log of each script just by clicking on the script name. 
  - The same report is also accessible in the heading of any of the "Scripts Status" web pages.  

### The Daily backup report:
![Daily Backup Report Example](/assets/img/man/sadm_daily_report/sadm_daily_report_backup.png)
  - This report present the last execution statistics of all the daily backup script (sadm_backup.sh) for each systems. Since the daily backup should have been taken during the night, they should all have the same execution date. If this is not the case, then the execution date cell will have a yellow background, to advise you that there may be a problem with the backup on that system. 
  - The daily backup report is also accessible in the heading of the "Daily Backup Status" web page.  
  - If you want to change the backup schedule of a particular system, just click on the "Schedule Activated" cell of the desired system.
  - If you want to see the log of the backup, click on the "Status" cell of the desired system.


### The [Rear](https://relax-and-recover.org/) system image backup:
![Daily ReaR Image Backup Report Example](/assets/img/man/sadm_daily_report/sadm_daily_report_rear.png)
  - The [ReaR](https://relax-and-recover.org/) report, show you the last ReaR backup execution result of each systems.
  - If a backup last execution date is older than 7 days, the date cell of that backup will have a yellow background. If the current backup size and the  previous backup differ than more than 50% they will have a yellow background as well. This may tell you that your backup have greater than expected and you may want to take a look at it.
  - If you want to change the backup schedule of a particular system just click on the "Schedule Activated" cell of the desired system.  
  - The ReaR daily report is also accessible in the heading of the "ReaR Backup Schedule Status" web page.
  - If you want to see the log of the backup, just click on the "Status" cell of the desired system.  
<br>
- **Excluding Servers or/and Scripts from the reports:**
    - At the beginning of the script you will find a section defining two variables named "**SCRIPTS**" and "**SERVERS**". You can used them to exclude some scripts from the "Daily Scripts Report" by specifying them in the "SCRIPTS" variable.
    - To exclude server(s) from ALL the reports, you can specify them in the "SERVERS" variable. Server name used MUST not include the domain name.
  
```bash
# Exclude template scripts and demo scripts.
SCRIPTS="sadm_template sadmlib_std_demo"
    
# Exclude System Startup/Shutdown Script for Daily scripts report
SCRIPTS="$SCRIPTS sadm_startup sadm_shutdown"

# Server excluded from ALL Daily Report (Backup, Rear and Scripts)
SERVERS="server1 server2"
```



## EXECUTION EXAMPLE

```bash
# sadm_daily_report.sh

Sun Jan 17 14:39:39 EST 2021 - sadm_daily_report.sh V1.20 - SADM Lib. V3.64
Server Name: robin.batcave.com - Type: LINUX
CENTOS 7.9.2009 Kernel 3.10.0-1160.11.1.el7.x86_64
==================================================

Creating the Daily Backup Report
[ OK ] Mount batnas.batcave.com:/volume1/backup_linux /mnt/daily_report
[ OK ] Create Backup Web Page.
[ OK ] Unmount directory /mnt/daily_report
[ OK ] Daily Backup Report sent to batman@batcave.com
End of Daily Backup Report ...

Creating the ReaR Backup Report
[ OK ] Mount batnas.batcave.com:/volume1/backup_rear /mnt/daily_report
[ OK ] Unmount directory /mnt/daily_report
[ OK ] ReaR Backup Report sent to batman@batcave.com
End of the ReaR Backup Report ...

Creating the Daily Script Report
[ OK ] Create Script Web Page.
[ OK ] No script actually running.
[ OK ] No script terminated with error
[ OK ] Scripts grouped by server page generated
[ OK ] Script report sent to batman@batcave.com
End of the Script Report ...

==================================================
Script exit code is 0 (Success) and execution time is 00:00:27
History (/sadmin/dat/rch/robin_sadm_daily_report.rch) trim to 35 lines ($SADM_MAX_RCLINE=35).
Script will send an alert only when it terminate with error ($SADM_ALERT_TYPE=1).
Script succeeded, no alert will be send to 'default' alert group.
New log (/sadmin/log/robin_sadm_daily_report.log) created ($SADM_LOG_APPEND='N').
End of sadm_daily_report.sh - Sun Jan 17 14:40:02 EST 2021
==========================================================================
```

## OPTIONS

| Options | Description                                |
|:-------:|:------------------------------------------ |
| **-d**  | Set Debug level from 0 to 9 (Default is 0) |
| **-h**  | Display this help and exit.                |
| **-v**  | Output version information and exit.       |
| **-r**  | Don't produce the ReaR backup report       |
| **-b**  | Don't produce the Daily backup report      |
| **-s**  | Don't produce the Scripts report           |


## REQUIREMENTS
- Environment variable 'SADMIN' need to be defined, it specify the root directory of the SADMIN tools (normally /opt/sadmin).  
  - It should be already done, the setup script have updated the /etc/profile.d/sadmin.sh and then /etc/environment files.  
- The [SADMIN main configuration file](/doc/man/file_sadmin_cfg.php), is needed and it's loaded in memory at the beginning of every scripts.
  - This file should already exist and contains your SADMIN configuration and preference setting.
- SADMIN Tools shell library, "$SADMIN/lib/sadmlib.sh".  


## EXIT STATUS

| Exit Code | Description                                           |
|:-------:|:-------------------------------------------             |
| 0       | An exit status of zero indicates success.               |  
| 1       | Failure is indicated by a nonzero value, typically ‘1’. |  


## AUTHOR
[Jacques Duplessis](jacques.duplessis@sadmin.ca.)  
Any suggestions or bug report can be submitted at the [support page](www.sadmin.ca/support.php)


## COPYRIGHT
Copyright © 2021 Free Software Foundation, Inc. License GPLv3+: [GNU GPL version 3 or later](http://gnu.org/licenses/gpl.html)  
This is free software, you are free to change and redistribute it.   
There is NO WARRANTY to the extent permitted by law.  


## SEE ALSO
The Daily Backup Script - [sadm_backup.sh](http://sadmin/www/doc/man/sadm_backup.html)



