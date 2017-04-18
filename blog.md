---
layout: page
title: Blog
description: this page lists all blog posts
---

<ul>
{% for post in site.posts %}
<li>
{{ post.date | date_to_string }} >> <a href="{{ post.url }}">{{ post.header | default: post.title }}</a>
</li>
{% endfor %}
</ul>