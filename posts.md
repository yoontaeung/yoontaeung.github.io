---
layout: default
title: All Posts
permalink: /posts/
---

<h1>{{ page.title }}</h1>

<ul class="post-list">
  {% for post in site.posts %}
    <li>
      <h2><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h2>
      <p>Published on {{ post.date | date: "%B %-d, %Y" }}</p>
      <p>{{ post.excerpt | strip_html }}</p>
    </li>
  {% endfor %}
</ul>