---
layout: default
title: List of my Ctf Writeups by Categories
permalink: /writeups
---

[<--back]({{ site.url }}{{ site.baseurl }})
<br>
## OverTheWire

<ul>
  {% for post in site.categories.overthewire %}
    <li>
      <a href="{{ site.url }}/{{ post.url }}">{{ post.title }}</a> -> {{ post.date | date: "%-d %B %Y" }}
    </li>
  {% endfor %}
</ul>
<br>

## Vulnhub

<ul>
  {% for post in site.categories.vulnhub %}
    <li>
      <a href="{{ site.url }}/{{ post.url }}">{{ post.title }}</a> -> {{ post.date | date: "%-d %B %Y" }}
    </li>
  {% endfor %}
</ul>

<!-- <br>

<ul>
{% for category in site.categories %}
<li>"{{ category | first }}"{% unless forloop.last %},{% endunless %}</li>
{% endfor %}

</ul> -->
<!-- 
<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ site.baseurl }}/{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>
 -->