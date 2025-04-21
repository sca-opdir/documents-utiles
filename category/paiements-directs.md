---
layout: default
title: Contrôles
permalink: /category/paiements-directs/
---

<h2>Articles de la catégorie : Paiements directs</h2>

<ul>
  {% assign controles = site.categories.Paiements-directs %}
  {% for post in controles %}
    <li>
      <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>
