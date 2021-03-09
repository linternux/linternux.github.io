---
layout: post
title: template
date: 2021-02-09 09:28:10
date_updated: 2020-02-09 
author: jacques
permalink: template
tags: users_administration password 
categories: 
  - User_admin
  - password
---


User is calling and say that he forgot is password. So you need to change his password and for security reason you want to force him to change it upon his first login. The command &#8220;chage -d 0&#8221; (change aging) will force user to change his password upon first login.

Change his password

```
# passwd josblow
```

Set expiration date to 0

```
# chage -d 0
```


 <br>
 Page updated : {{ page.date_updated }}
 <br>Categories : 
 {% for cat in page.categories %}
    {{ page.cat }}
 {% endfor %}
 <br>Tags : {{ page.tags }}
 
<br>
<hr>
Liste des Pages
<ul>
{% for post in site.pages %}
    <li><a style="{% if page.url == post.url %}color:red;{%endif %}" href="{{ post.url }}">{{post.title}}</a></li>
{% endfor %}
</ul>
<br>

<br>
<hr>
Liste des Posts
<ul>
{% for post in site.posts %}
    <li><a href="{{ post.url }}">{{post.title}}</a></li>
{% endfor %}
</ul>

<br>
<hr>
{% if page.title == "Template"%}
  This is a template post, with Capital T
{% elsif page.title == "template" %}
   This is the second template without Capital t
{% else %}
    The is another Post
{% endif %}


<br>
<hr>
{% assign image_files = site.static_files | where: "image", true %}
{% for myimage in image_files %}
  {{ myimage.path }} {{ myimage.modified_time }} 
  {{ myimage.name }} {{ myimage.basename }} {{ myimage.extname }}
{% endfor %}

<br>
<hr>
## Get file attributes
{% assign files = site.static_files %}
{% for wfile in files %}
  {{ wfile.path }} {{ wfile.modified_time }} 
  {{ wfile.name }} {{ wfile.basename }} {{ wfile.extname }}
{% endfor %}


<br>
<hr>
## Page Date 1
{% assign m = page.date | date: "%-m" %}
{{ page.date | date: "%-d" }}
{% case m %}
  {% when '1' %}January
  {% when '2' %}February
  {% when '3' %}Mars
  {% when '4' %}April
  {% when '5' %}Mai
  {% when '6' %}June
  {% when '7' %}July
  {% when '8' %}August
  {% when '9' %}September
  {% when '10' %}October
  {% when '11' %}November
  {% when '12' %}December
{% endcase %}
{{ page.date | date: "%Y" }}


<br>
<hr>
## Page Date/Time Format
String  - {{ page.date | date_to_string }}
<br>
MM/DD/YYYY - {{ page.date | date: "%m/%d/%Y" }}
<br>
DD/MM/YYYY - {{ page.date | date: "%d/%m/%Y" }}
<br>
%c  - {{ page.date | date: "%c" }}
<br>
%R  - {{ page.date | date: "%R" }}
<br>
{% assign day = page.date | date: "%-d"  %}
{% case day %}
  {% when '1' or '21' or '31' %}{{ day }}st
  {% when '2' or '22' %}{{ day }}nd
  {% when '3' or '23' %}{{ day }}rd
  {% else %}{{ day }}th
{% endcase %}
{{ page.date | date: "of %B, %Y" }}

<br>
<hr>
Navigation
<nav>
  {% for item in site.data.navigation %}
    <a href="{{ item.link }}" {% if item.highlight %}style="border: 1px solid red;"{% endif %} {% if item.link == page.url %}class="active"{% endif %}>
      {{ item.name }}
    </a>
  {% endfor %}
</nav>

<br>
<hr>
## Site by Category
{% for category in site.categories %}
  <h2>{{ category[0] }}</h2>
  <ul>
    {% for post in category[1] %}
      <li><a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
  </ul>
{% endfor %}

<br>
<hr>
## Site by Tag
{% for tag in site.tags %}
  <h2>{{ tag[0] }}</h2>
  <ul>
    {% for post in tag[1] %}
      <li><a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
  </ul>
{% endfor %}
