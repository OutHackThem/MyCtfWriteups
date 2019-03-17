---
layout: default
---

## Welcome to another page


{% for post in site.posts %}
<a href="{{ post.url }}">{{ post.title }}</a>
{% endfor %}

_yay_

[back](./)
