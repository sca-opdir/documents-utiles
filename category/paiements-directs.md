---
layout: default
title: Paiements-directs
permalink: /category/paiements-directs/
---

<h2>Articles de la catégorie : Paiements directs</h2>

<ul>
  {% assign controles = site.categories.Paiements-directs %}
  		{% include listings.html listings=controles %}
<!--
  {% for post in controles %}
    <li>
      <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
-->
</ul>
