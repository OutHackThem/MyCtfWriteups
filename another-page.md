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

<p>{{ site.baseurl }}</p>
_yay_

[back](./)
