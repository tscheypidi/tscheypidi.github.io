---
layout: page
title: Tutorials
description: A collection of tutorials to get started
image: assets/images/pic09.jpg
nav-menu: true
---

{% for tutorial in site.tutorials %}
  <blockquote>
  <h4><a href="{{tutorial.url}}">{{tutorial.title}}</a>
{% if tutorial.level == 0 %}
  ⚪⚪⚪⚪⚪ <span style="color:purple">All levels
{% elsif tutorial.level == 1 %}
  ⚫⚪⚪⚪⚪ <span style="color:green">Beginner
{% elsif tutorial.level == 2 %}
  ⚫⚫⚪⚪⚪ <span style="color:green">Beginner
{% elsif tutorial.level == 3 %}
  ⚫⚫⚫⚪⚪ <span style="color:orange">Advanced
{% elsif tutorial.level == 4 %}
  ⚫⚫⚫⚫⚪ <span style="color:orange">Advanced
{% elsif tutorial.level == 5 %}
  ⚫⚫⚫⚫⚫ <span style="color:red">Expert
{% endif %} </span></h4>
  <ul>
  {% for lesson in tutorial.lessonsContent %}
    <li>{{ lesson | markdownify }}</li>
  {% endfor %}
  </ul>
  </blockquote>
{% endfor %}
