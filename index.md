---
layout: default
title: Repositories
---

<div class="controls">
  <input id="q" type="search" placeholder="Search repositories… (name, description, language)">
  <select id="sort">
    <option value="updated">Sort: Recently updated</option>
    <option value="stars">Sort: Stars</option>
    <option value="name">Sort: Name (A–Z)</option>
  </select>
</div>

<div id="grid" class="grid">
{% assign repos = site.github.public_repositories
  | where_exp: "r", "r.fork == false"
  | where_exp: "r", "r.archived == false"
  | where_exp: "r", "r.name != 'tobiasanalytica.github.io'"%}

{% comment %} Pre-fill update time as a Unix timestamp for simple sorting in JS {% endcomment %}
{% assign repos = repos | sort: "pushed_at" | reverse %}
{% for repo in repos %}
  <article class="card"
    data-name="{{ repo.name | downcase }}"
    data-stars="{{ repo.stargazers_count }}"
    data-lang="{{ repo.language | downcase }}"
    data-updated="{{ repo.pushed_at | date: '%s' }}">
    <h3><a class="title" href="{{ repo.html_url }}">{{ repo.name }}</a></h3>
    <p class="desc">
      {% if repo.description %}{{ repo.description }}{% else %}<em>No description yet.</em>{% endif %}
    </p>
    {% if repo.topics and repo.topics.size > 0 %}
      <div>
        {% for t in repo.topics limit:4 %}
          <span class="topic">#{{ t }}</span>
        {% endfor %}
      </div>
    {% endif %}
    <div class="meta">
      <span class="star">⭐ {{ repo.stargazers_count }}</span>
      {% if repo.language %}<span class="badge">{{ repo.language }}</span>{% endif %}
      <span class="updated">Updated: {{ repo.pushed_at | date: "%Y-%m-%d" }}</span>
    </div>
  </article>
{% endfor %}
</div>

<script>
  const q = document.getElementById('q');
  const sortSel = document.getElementById('sort');
  const grid = document.getElementById('grid');

  function cards(){ return Array.from(grid.querySelectorAll('.card')); }

  function filter(){
    const term = (q.value || '').trim().toLowerCase();
    cards().forEach(c => {
      const text = (c.innerText || '').toLowerCase();
      c.style.display = text.includes(term) ? '' : 'none';
    });
  }

  function sort(){
    const mode = sortSel.value;
    const visible = cards().filter(c => c.style.display !== 'none');
    visible.sort((a,b) => {
      if(mode === 'stars'){
        return (+b.dataset.stars) - (+a.dataset.stars);
      } else if(mode === 'name'){
        return a.dataset.name.localeCompare(b.dataset.name, 'en');
      } else {
        return (+b.dataset.updated) - (+a.dataset.updated);
      }
    });
    visible.forEach(el => grid.appendChild(el));
  }

  q.addEventListener('input', () => { filter(); sort(); });
  sortSel.addEventListener('change', sort);

  // initial
  filter(); sort();
</script>
