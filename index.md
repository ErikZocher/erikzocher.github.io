---
layout: default
title: "Erik Zocher's Blog"
---

# 👋 Hello!

I'm Erik, a senior software engineer and design enthusiast living in Berlin. Welcome to my personal blog, where I write about:

- 🤖 **AI Agents & Automation** - Building with Hermes, tool use, and what AI can actually do
- 🐧 **Raspberry Pi & Homelab** - Headless setups, NVMe boot, always-on services
- 🚋 **Berlin** - Events, Trams, City Life
- 🇪🇸 **Learning Spanish** - Vocabulary, Grammar, Culture

I built a personal AI agent named [Puck](https://erikzocher.github.io/technology/2026/07/28/meet-puck.html) that runs on a Raspberry Pi in my living room. It watches the tram schedule, keeps my wall dashboard updated, and helps me write this blog.

## Posts

{% for post in site.posts limit:5 %}
### [{{ post.title }}]({{ post.url }})
*{{ post.date | date: "%Y-%m-%d" }} · {% for tag in post.tags %}`{{ tag }}` {% endfor %}{% endfor %}

[🔍 Search](suche) · [🏷️ Tags](tags)

---

**📡 Follow the blog via [RSS feed](feed.xml)**
