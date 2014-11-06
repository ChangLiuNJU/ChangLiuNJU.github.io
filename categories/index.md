---
layout: default
title:  Categories
---

<ul>
{% for cat in site.categories %}
  <li><strong>{{ cat[0] }}</strong></li>
  {% for post in cat[1] %}
    <time>{{ post.date | date:"%Y-%m-%d" }}</time>
    <a href="{{ site.url }}{{ post.url }}">{{ post.title }}</a><br>
  {% endfor %}
  <br/>
{% endfor %}
</ul>