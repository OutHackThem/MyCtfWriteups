---
layout: default
permalink: /about
---


<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ site.baseurl }}/{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>

<p>{{ site.url }}{{ site.baseurl }}</p>

### OverTheWire

<ul>
  {% for post in site.categories.overthewire %}
    <li>
      <a href="{{ site.baseurl }}/{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>

_yay_

[back]({{ site.url }}{{ site.baseurl }})
