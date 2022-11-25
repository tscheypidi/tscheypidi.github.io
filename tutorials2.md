---
layout: post
title: Tutorials Backup
description: A collection of tutorials to get started
image: assets/images/generic/pic02w.jpg
nav-menu: true
---

{% for tutorial in site.tutorials %}
 <div class="box">
  <h4>
{% if tutorial.level == 0 %}
  ⚫⚫⚫⚫⚫ 
{% elsif tutorial.level == 1 %}
  ⚪⚫⚫⚫⚫
{% elsif tutorial.level == 2 %}
  ⚪⚪⚫⚫⚫ 
{% elsif tutorial.level == 3 %}
  ⚪⚪⚪⚫⚫ 
{% elsif tutorial.level == 4 %}
  ⚪⚪⚪⚪⚫
{% elsif tutorial.level == 5 %}
  ⚪⚪⚪⚪⚪ <span style="color:red">Expert
{% endif %} <a href="{{tutorial.url}}">{{tutorial.title}}</a> 
{% if tutorial.level == 0 %}
 <span style="color:#8d82c4">All levels
{% elsif tutorial.level == 1 %}
 <span style="color:#87c5a4">Beginner
{% elsif tutorial.level == 2 %}
 <span style="color:#87c5a4">Beginner
{% elsif tutorial.level == 3 %}
 <span style="color:#e7b788">Advanced
{% elsif tutorial.level == 4 %}
 <span style="color:#e7b788">Advanced
{% elsif tutorial.level == 5 %}
 <span style="color:#ec8d81">Expert
{% endif %} </span>
</h4>
  <ul>
  {% for lesson in tutorial.lessonsContent %}
    <li>{{ lesson | markdownify | remove: '<p>' | remove: '</p>' }}</li>
  {% endfor %}
  </ul>
  </div>
{% endfor %}
