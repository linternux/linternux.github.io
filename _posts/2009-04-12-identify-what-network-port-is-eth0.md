---
id: 20
title: Identify what network port is eth0 ?
date: 2009-04-12T21:05:01+00:00
author: jacques
layout: post
guid: http://blog.linternux.com/?p=20
permalink: /index.php/2009/04/12/identify-what-network-port-is-eth0/
categories:
  - Network
tags:
  - ethtool
---
You have two network cables plug in the back of a server and you want to know which one is eth0? Use the following command and the interface light will start blinking (for 10 seconds) on the chosen adapter.

> \# ethtool -p eth0 10 

This command can be run without disturbing the traffic on the interface.