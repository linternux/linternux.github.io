---
layout: page
title: Force user to change password
date: 2021-03-11 09:28:10
date_updated: 2021-03-11 09:29:10 
tags: usersadministration password 
categories: useradministration password
---


User is calling and say that he forgot is password. So you need to change his password and for security reason you want to force him to change it upon his first login. The command &#8220;chage -d 0&#8221; (change aging) will force user to change his password upon first login.  
<br>
<img src="/assets/img/sadm_password.jpg" class="align-left" alt="Password Image">
Image by <a href="https://pixabay.com/users/absolutvision-6158753/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=2781614">Gino Crescoli</a> from <a href="https://pixabay.com/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=2781614">Pixabay</a>

<br>
Change the user password, because he forgot it. 

{% highlight bash %}
# passwd josblow
{% endhighlight %}
<br>

Then we set expiration date to 0, so when user login with his new password, he will have to change it before continuing.
{% highlight bash %}
# chage -d 0 josblow
{% endhighlight %}

<br>
So when the user log on the server, he will need to change his password before going forward.
{% highlight bash %}
$ ssh josblow@raspi1
josblow@raspi1's password:
You are required to change your password immediately (administrator enforced)
Linux raspi1.maison.ca 5.10.17+ #1403 Mon Feb 22 11:26:13 GMT 2021 armv6l

WARNING: Your password has expired.
You must change your password now and login again!
Changing password for josblow.
Current password:
New password:
Retype new password:
passwd: password updated successfully
Connection to raspi1 closed.

$ ssh josblow@raspi1
josblow@raspi1's password:
Linux raspi1.maison.ca 5.10.17+ #1403 Mon Feb 22 11:26:13 GMT 2021 armv6l
josblow@raspi1:~ $
{% endhighlight %}
<br>
