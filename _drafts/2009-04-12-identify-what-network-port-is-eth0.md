---
title: Identify physical network port name
date: 2021-03-30 10:32:00
layout: post
categories:
  - Network
tags:
  - ethtool
---

You have two network cables plug in the back of a server and you want to know which one is eth0? Use the following command and the interface light will start blinking (for 10 seconds) on the chosen adapter.

> \# ethtool -p eth0 10 

This command can be run without disturbing the traffic on the interface.