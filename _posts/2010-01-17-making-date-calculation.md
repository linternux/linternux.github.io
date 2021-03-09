---
title: Making Date Calculation
date: 2021-02-28 10:03:03
layout: page
categories:
  - Scripting 
tags:
  - Date
---

You&#8217;re doing a script and you need to know what was the date 35 days ago or what will the date be in 21 days. This is where  the &#8220;date&#8221; command can help you doing that kind of calculation. In this little article we will present some examples to help us making some data calculation. I would like to point out, that all of these date calculation take in consideration the leap year.   
<br>
<img src="/assets/img/sadm_calendar.jpg" class="align-left" alt="">
Image by <a href="https://pixabay.com/users/amber_avalona-1512238/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=2290045">Amber Avalona</a> from <a href="https://pixabay.com/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=2290045">Pixabay</a>

Revision 1.1 - 2 March 2021 - [Download a pdf version](/assets/pdf/making-date-calculation.pdf)   

## Current date and time

| Command 	                              | Description                         | Result                        |
| :---                                    | :---                                | :---                          |
| date                                    | Get/Show current date & time        | Sun 28 Feb 2021 17:05:41 EST  |
| date "+%C%y.%m.%d %H:%M:%S"             | Current Date/Time YYYY.MM.DD HH:MM:SS| 2021.02.28 21:09:17          | 
| date "+%C%y.%m.%d %T"                   | Current Date/Time YYYY.MM.DD HH:MM:SS| 2021.02.28 21:09:17          | 
| date '+%C%y.%m.%d'                      | Current Date/Time Format YYYY.MM.DD | 2021.02.28                    |
| date "+%j"                              | Day of year (001..366)              | 059                           | 
| date +%q                                | Quarter of year (1..4)              | 1                             | 
| date +%u                                | Day of week (1..7); 1 is Monday     | 7                             | 
| date +%w                                | Day of week (0..6); 0 is Sunday     | 0                             |
| date +%U                                | week number of year (00..53)        | 09                            |
| date +%d                                | Day of month (e.g., 01)             | 28                            | 
| date +%m                                | Month number (01..12)               | 02                            | 
| date +%y                                | Last two digits of year (00..99)    | 21                            |     
| date +%Y                                | Current Year with century           | 2021                          | 
| date +%H                                | Current Hour (00..23)               | 21                            |
| date +%M                                | Current Minute (00..59)             | 09                            |
| date +%S                                | Current second (00..60)             | 41                            |


<br>
## Epoch time

| Command 	                              | Description                         | Result                        |
| :---                                    | :---                                | :---                          |
| date                                    | Get current date & time             |Tue 02 Mar 2021 10:42:09 AM EST |
| date +%s                                | Get current epoch time              | 1614699732                    | 
| date -d @1614699732 "+%Y/%m/%d %T"      | Convert Epoch Time to a Date format | 2021/03/02 10:42:12           | 
| date --date='03/02/2021 10:42:12' +%s   | Convert a Date to an Epoch Time     | 1614699732                    |


<br>
## Date in the future

| Command 	                              | Description                         | Result                        |
| :---                                    | :---                                | :---                          |
| date --date="35 days"                   | Date/Time in 35 days                | Sun Apr  4 18:12:51 EDT 2021  |
| date --date="1 month"                   | Date/Time in one month              | Sun Mar 28 18:58:26 EDT 2021  | 
| date --date="this friday"               | Date/Time Next Friday               | Fri Mar  5 00:00:00 EST 2021  |
| date --date="next day"                  | Date/Time Tomorrow                  | Mon Mar  1 18:01:42 EST 2021  |
| date --date="tomorrow"                  | Date/Time Tomorrow                  | Mon Mar  1 18:01:42 EST 2021  |
| date --date='fortnight'                 | Date/Time 15 days ahead             | Sun Mar 14 21:30:30 EDT 2021  |
| date --date='5 fortnight'               | Date/Time 5 times 15 days ahead     | Sun May  9 21:38:33 EDT 2021  |
| date --date='2 hour'                    | Date/Time 2 hours from now          | Sun Feb 28 22:43:30 EST 2021 |
| date --date='20 minute'                 | Date/Time 20 minutes from now       | Sun Feb 28 21:04:49 EST 2021 |
| date -dmonday +%Y/%m/%d                 | Date of next Monday                 | 2021/03/01                   |
| date -d'monday-fortnight ago' +%Y%m%d   | Last Monday - 2 weeks from now      | 20210215                     | 
| date --date "next month - $(date +%d) day" | Last Day of Month                | Wed 31 Mar 2021 10:30:21 AM EDT |
| date -dnext-monday +"%Y/%m/%d"          | Date/Time of next Monday            | 2021/03/01                   |
| date -d'monday+fortnight' +"%Y/%m/%d"   | Next Monday + 2 weeks from now      | 2021/02/15                   |   
| date -d'52+monday' +"%Y%m%d"            | Date of 52th Monday from now        | 20220221                     |       
| date -d'monday+14 days' +"%Y%m%d"       | Date of next Monday + 14 Days       | 20210315                     |

<br>
## Date in the past

| Command 	                              | Description                         | Result                       |
| :---                                    | :---                                | :---                         |
| date --date='20 minutes ago'            | Date/Time 20 minutes ago            | Sun Feb 28 20:25:25 EST 2021 |
| date --date='2 hours ago'               | Date/Time 2 hours ago               | Sun Feb 28 18:44:20 EST 2021 | 
| date --date='10 days ago'               | Date/Time 10 days ago               | Thu Feb 18 20:51:01 EST 2021 |
| date --date='10 weeks ago'              | Date/Time 10 weeks ago              | Sun Dec 20 20:51:28 EST 2020 | 
| date --date='10 months ago'             | Date/Time 10 months                 | Tue Apr 28 21:52:27 EDT 2020 |
| date --date='10 year ago'               | Date/Time 10 years ago              | Mon Feb 28 20:52:54 EST 2011 | 
| date --date="yesterday" +"%Y/%m/%d %T"  | Yesterday Date                      | 2021/02/27 20:49:45          |
| date --date='yesterday'                 | Yesterday Date/Time                 | Sat Feb 27 20:49:49 EST 2021 |
| date --date='fortnight ago'             | Date/Time 15 days ago               | Sun Feb 14 20:40:06 EST 2021 |
| date -dlast-monday                      | Date/Time of Last Monday            | Mon Feb 22 00:00:00 EST 2021 |
| date -dlast-monday +"%Y/%m/%d"          | Date Last Monday                    | 2021/02/22                   |
| date -d'monday-14 days' +"%Y/%m/%d %T"  | Two Mondays ago                     | 20210215                     |
