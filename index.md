---
layout: default
title: Erik Zocher — Blog
---

# 👋 Hallo!

Willkommen auf meinem persönlichen Blog. Hier schreibe ich über:

- 🎨 **Design & Kreativität** — Grafikdesign, Art Direction, UX/UI
- 💻 **Technik** — AI Agents, Raspberry Pi, Coding
- 🚋 **Berlin** — Events, Trams, Stadtleben
- 🇪🇸 **Spanisch lernen** — Vokabeln, Grammatik, Kultur

## Beiträge

{% for post in site.posts limit:5 %}
### [{{ post.title }}]({{ post.url }})
*{{ post.date | date: "%d.%m.%Y" }}* — {% for tag in post.tags %}`{{ tag }}` {% endfor %}{% endfor %}

[🔍 Blog durchsuchen →](suche)
