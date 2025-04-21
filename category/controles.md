---
layout: default
title: Contrôles
permalink: /category/contrôles/
---

<h2>Articles de la catégorie : Contrôles</h2>

<ul>
  {% assign controles = site.categories.Controles %}
  {% for post in controles %}
    <li>
      <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>
