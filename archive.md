---
layout: default
title: Archive
permalink: /archive/
---

# 🗂️ Archive

{% assign posts_by_year = site.posts | group_by_exp: "post", "post.date | date: '%Y'" %}

{% for year in posts_by_year %}
## {{ year.name }}

{% for post in year.items %}
- **{{ post.date | date: "%d.%m.%Y" }}** - [{{ post.title }}]({{ post.url }}) {% for tag in post.tags %}`{{ tag }}` {% endfor %}
{% endfor %}

{% endfor %}
