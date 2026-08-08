---
layout: page
title: Notes
permalink: /notes/
---

Short findings. What I was looking at, what I found, how I know, and what I'm
not claiming.

<ul class="index">
{% for post in site.posts %}
  <li>
    <span class="when">{{ post.date | date: "%d %B %Y" }}</span>
    <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
    {% if post.tags.size > 0 %}
      <span class="filed">{{ post.tags | join: " &middot; " }}</span>
    {% endif %}
  </li>
{% endfor %}
</ul>
