---
title: Identify physical network port name
date: 2021-03-30 10:30:02
layout: post
categories:
  - Network
tags:
  - ethtool
---

The 'ethtool' is a command line utility used to query and control network device driver and 
hardware settings, particularly for wired Ethernet devices. In our example, we will use it to flash
the light on the back of a card, this will be useful to identify the device name that belong to 
what port on the back of you Linux computer. 

<!--more-->

<br>
<img src="/assets/img/sadm_network_light.jpg" class="align-center" alt="">

<center>
Image by <a href="https://pixabay.com/users/martinelle-495565/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=1572617">Martinelle</a> from <a href="https://pixabay.com/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=1572617">Pixabay</a>
</center><br>


Let's say you have two network cables plug in on the back of a 
server and you want to know which one is eth0. Use the following command and the interface light 
will start blinking (for 10 seconds) on the chosen adapter.
<br>

```bash
# ethtool -p eth0 10 
```

This command can be executed without disturbing the traffic on the interface.