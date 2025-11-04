---
layout: default
title: All My Repositories
---

# All My Public Repositories

<ul>
{% assign repos = site.github.public_repositories
  | where_exp: "r", "r.fork == false"
  | where_exp: "r", "r.archived == false"
  | sort: "pushed_at" | reverse %}
{% for repo in repos %}
  <li>
    <a href="{{ repo.html_url }}">{{ repo.name }}</a>
    — ⭐ {{ repo.stargazers_count }}
    {% if repo.language %} — {{ repo.language }}{% endif %}
    {% if repo.description %} — {{ repo.description }}{% endif %}
    <small> · Updated {{ repo.pushed_at | date: "%Y-%m-%d" }}</small>
  </li>
{% endfor %}
</ul>
