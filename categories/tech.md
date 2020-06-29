---
layout: page
title: Tech
permalink: /blog/categories/tech
---
 
<h5> Posts by Category : {{ page.title }} </h5>

<div class="card">
{% for post in site.categories.tech %}
 {% if post.title != "ignore" %}
  <li class="category-posts"><span>{{ post.date | date_to_string }}</span> &nbsp; <a href="{{ post.url }}">{{ post.title }}</a></li>
 {% endif %}
{% endfor %}
</div>






