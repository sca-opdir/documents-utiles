---
layout: default
title: Autres thèmes
permalink: /category/autres/
---

<h2>Articles de la catégorie : Autres</h2>

<ul>
  {% assign controles = site.categories.Autres %}
  		{% include listings.html listings=controles %}
 <!-- {% for post in controles %}
    <li>
      <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
-->
</ul>
