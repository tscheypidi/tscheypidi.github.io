---
title: Tutorials
layout: landing
description: 'A collection of tutorials to get started'
image: assets/images/generic/pic07.jpg
nav-menu: true
show_tile: true
---

<!-- Main -->
<div id="main">

<!-- One -->
<section id="one">
	<div class="inner">
		<header class="major">
			<h2>Introduction to MAgPIE and MADRaT</h2>
		</header>
		<p>The following is the collection of tutorials which help in getting accquainted with the MAgPIE global land system framework as well as with the MADRaT data preprocessing framework. The latter is used in MAgPIE to preprocess the input data, but also can be used independently for other data processing tasks.</p>
		<p>Tutorials are listed in the recommended order. Difficulty and recommended experience level are indicated in the title.</p>
		<ul class="actions">
          <li><a href="#two" class="button next scrolly">Get Started</a></li>
        </ul>
	</div>
</section>

<!-- Two -->
<section id="two" class="spotlights">
{% for tutorial in site.tutorials %}
<section>
		<a href="{{tutorial.url}}" class="image">
			<img src="{% link assets/images/generic/pic01.jpg %}" alt="" data-position="center center" />
		</a>
		<div class="content">
			<div class="inner">
				<header class="major">
					<h3>{% if tutorial.level == 0 %}
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
{% endif %} {{tutorial.title}}
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
</h3>
				</header>
				<p><ul>
  {% for lesson in tutorial.lessonsContent %}
    <li>{{ lesson | markdownify | remove: '<p>' | remove: '</p>' }}</li>
  {% endfor %}
  </ul></p>
				<ul class="actions">
					<li><a href="{{tutorial.url}}" class="button">Start tutorial</a></li>
				</ul>
			</div>
		</div>
	</section>
{% endfor %}
</section>

<!-- Three -->
<section id="three">
	<div class="inner">
		<header class="major">
			<h2>Massa libero</h2>
		</header>
		<p>Nullam et orci eu lorem consequat tincidunt vivamus et sagittis libero. Mauris aliquet magna magna sed nunc rhoncus pharetra. Pellentesque condimentum sem. In efficitur ligula tate urna. Maecenas laoreet massa vel lacinia pellentesque lorem ipsum dolor. Nullam et orci eu lorem consequat tincidunt. Vivamus et sagittis libero. Mauris aliquet magna magna sed nunc rhoncus amet pharetra et feugiat tempus.</p>
		<ul class="actions">
			<li><a href="generic.html" class="button next">Get Started</a></li>
		</ul>
	</div>
</section>

</div>
