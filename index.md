---
layout: page
title: Blog Index
tagline: Supporting tagline
---
{% include JB/setup %}

    
<br>
## New Posts List
<br>


<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>




