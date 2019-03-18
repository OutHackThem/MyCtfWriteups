---
layout: default
title: List of my Ctf Writeups by Categories
permalink: /writeups
---

[<--back]({{ site.url }}{{ site.baseurl }})
<br>
### OverTheWire

<ul>
  {% for post in site.categories.overthewire %}
    <li>
      <font size=2px>{{ page.date | date: "%-d %B %Y" }}</font><a href="{{ site.baseurl }}/{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>

_yay_


<!-- <ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ site.baseurl }}/{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul> -->
