---
id: 11
title: Epoch time date calculation
date: 2009-04-08T20:14:39+00:00
author: jacques
layout: post
guid: http://blog.linternux.com/?p=11
permalink: /index.php/2009/04/08/epoch-time-date-calculation/
categories:
  - Scripting
tags:
  - epoch time
---
In the Unix/Linux world we always come across at one point with the term &#8220;epoch time&#8221;. The epoch time is actually the number of seconds elapsed since midnight of January 1, 1970. Some application record the date and time in &#8220;epoch time&#8221;. Squid proxy server, for an example will record date and time entries in its log in epoch time.

I think we will all agree, that working with this kind of date format is not very meaningful for us. So, if you ever need to convert an epoch time format to a more readable format, here are some example using the bash shell.

#### Convert epoch time to date format

<pre><span style="font-family: monospace; font-size: x-medium;"><code># date -d @1221256800 "+%Y-%m-%d %T"
 2008-09-13 00:00:00</code>
</span></pre>

#### Convert date to epoch time

    # date -d "20080913" +%s"
     1221256800