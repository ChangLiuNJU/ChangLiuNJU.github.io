---
layout: default
title:  categories
---

<ul>
{% for cat in site.categories %}
  <li>{{ cat[0] }}</li>
  {% for post in cat[1] %}
    <time>{{ post.date | date:"%Y-%m-%d" }}</time>
    <a href="{{ site.url }}{{ post.url }}">{{ post.title }}</a><br>
  {% endfor %}
{% endfor %}
</ul>