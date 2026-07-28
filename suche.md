---
layout: default
title: Suche
---

# 🔍 Blog durchsuchen

<input type="text" id="search-input" placeholder="Suchbegriff eingeben..." style="width:100%;padding:12px;font-size:16px;border:2px solid #ccc;border-radius:6px;margin-bottom:20px;" onkeyup="searchPosts()">

<div id="search-results"></div>

<script>
var posts = [];
fetch('/search.json')
  .then(r => r.json())
  .then(data => { posts = data; });

function searchPosts() {
  var q = document.getElementById('search-input').value.toLowerCase().trim();
  var results = document.getElementById('search-results');
  
  if (q.length < 2) {
    results.innerHTML = '<p style="color:#999;">Gib mindestens 2 Zeichen ein.</p>';
    return;
  }
  
  var matches = posts.filter(function(p) {
    return p.title.toLowerCase().includes(q)
        || p.tags.some(function(t) { return t.includes(q); })
        || p.categories.some(function(c) { return c.includes(q); })
        || p.content.toLowerCase().includes(q);
  });
  
  if (matches.length === 0) {
    results.innerHTML = '<p>Keine Ergebnisse gefunden.</p>';
    return;
  }
  
  var html = '<p>' + matches.length + ' Treffer:</p>';
  matches.forEach(function(m) {
    html += '<div style="border:1px solid #ddd;border-radius:6px;padding:12px;margin:8px 0;">';
    html += '<h3><a href="' + m.url + '">' + m.title + '</a></h3>';
    html += '<small>' + m.date + ' — Tags: ' + m.tags.join(', ') + '</small>';
    html += '</div>';
  });
  results.innerHTML = html;
}
</script>
