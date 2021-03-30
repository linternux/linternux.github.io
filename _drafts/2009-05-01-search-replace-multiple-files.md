---
id: 110
title: Search and replace multiple files
date: 2009-05-01T18:08:19+00:00
author: jacques
layout: post
guid: http://linternux.com/?p=110
permalink: /index.php/2009/05/01/search-replace-multiple-files/
categories:
  - 'Tips &amp; Tricks'
tags:
  - oneliner
  - perl
---
In the example below we are replacing every occurrence of mama with papa in all .html files in current path using Perl.

<pre>perl -pi -e "s/mama/papa/g;" *.html</pre>

We do the same thing here but make a copy of original file for backup.

<pre>perl -pi -e.bak "s/mama/papa/g;" *.html</pre>