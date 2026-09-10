---
layout: page
title: "Projects"
permalink: /menu/projects.html
---

<ul class="posts">
  {% for project in site.projects %}
    <li>
      <h3>
        <a href="{{ project.url | relative_url }}">
          {{ project.title }}
        </a>
      </h3>
      {% if project.description %}
        <p>{{ project.description }}</p>
      {% endif %}
    </li>
  {% endfor %}
</ul>