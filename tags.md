---
layout: default
title: Tags
---

# 🏷️ Tags

{% assign all_tags = "" | split: "" %}
{% for post in site.posts %}
  {% for tag in post.tags %}
    {% assign all_tags = all_tags | push: tag %}
  {% endfor %}
{% endfor %}
{% assign unique_tags = all_tags | uniq | sort %}

<div id="tag-cloud">
{% for tag in unique_tags %}
  {% assign count = all_tags | where_exp: "t", "t == tag" | size %}
  <a href="javascript:void(0)" onclick="filterByTag('{{ tag }}')" class="tag-badge">
    #{{ tag }} ({{ count }})
  </a>
{% endfor %}
</div>

<hr>

<div id="tag-posts">
{% for tag in unique_tags %}
  {% assign count = all_tags | where_exp: "t", "t == tag" | size %}
  <div class="tag-group" id="tag-{{ tag | slugify }}" style="display:none;">
    <h2>#{{ tag }} ({{ count }})</h2>
    {% for post in site.posts %}
      {% if post.tags contains tag %}
        <p><a href="{{ post.url }}">{{ post.title }}</a> <small>({{ post.date | date: "%Y-%m-%d" }})</small></p>
      {% endif %}
    {% endfor %}
  </div>
{% endfor %}
</div>

<script>
function filterByTag(tag) {
  document.querySelectorAll('.tag-group').forEach(function(el) {
    el.style.display = 'none';
  });
  document.getElementById('tag-' + tag.replace(/\s+/g, '-').toLowerCase()).style.display = 'block';
  window.scrollTo(0, document.getElementById('tag-posts').offsetTop - 20);
}

// Auto-show tag from URL
var params = new URLSearchParams(window.location.search);
var tagParam = params.get('tag');
if (tagParam) {
  filterByTag(tagParam);
}

document.querySelectorAll('.tag-badge').forEach(function(el) {
  el.style.cssText = 'display:inline-block;padding:4px 10px;margin:4px;background:#f0f0f0;border-radius:4px;color:#333;text-decoration:none;font-size:14px;';
});
</script>
