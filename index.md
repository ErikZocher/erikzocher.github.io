---
layout: default
title: Erik Zocher — Blog
---

# 👋 Hello!

Welcome to my personal blog. I write about:

- 🎨 **Design & Creativity** — Graphic Design, Art Direction, UX/UI
- 💻 **Technology** — AI Agents, Raspberry Pi, Coding
- 🚋 **Berlin** — Events, Trams, City Life
- 🇪🇸 **Learning Spanish** — Vocabulary, Grammar, Culture

## Posts

{% for post in site.posts limit:5 %}
### [{{ post.title }}]({{ post.url }})
*{{ post.date | date: "%Y-%m-%d" }}* — {% for tag in post.tags %}`{{ tag }}` {% endfor %}{% endfor %}

[🔍 Search →](suche)
