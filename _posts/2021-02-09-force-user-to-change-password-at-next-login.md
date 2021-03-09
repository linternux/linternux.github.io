---
layout: post
title: Force user to change password
date: 2021-02-09 09:28:10
date_updated: 2020-02-09 
tags: usersadministration password 
categories: useradministration password
---


User is calling and say that he forgot is password. So you need to change his password and for security reason you want to force him to change it upon his first login. The command &#8220;chage -d 0&#8221; (change aging) will force user to change his password upon first login.

Change his password

{% highlight bash %}
# passwd josblow
{% endhighlight %}

Set expiration date to 0

{% highlight bash %}
# chage -d 0
{% endhighlight %}


 