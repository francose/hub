---
layout: page
title: Notes
permalink: /notes/
---

Short findings. What I was looking at, what I found, how I know, and what I'm
not claiming.

<ul>
{% for post in site.posts %}
  <li>
    <span>{{ post.date | date: "%Y-%m-%d" }}</span>
    &nbsp;&middot;&nbsp;
    <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
    {% if post.tags.size > 0 %}
      <br><small>{{ post.tags | join: ", " }}</small>
    {% endif %}
  </li>
{% endfor %}
</ul>
