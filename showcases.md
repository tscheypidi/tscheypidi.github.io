---
title: Showcases
description: 'Showcase applications of the model'
layout: landing
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
			<h2>A collection of MAgPIE showcases</h2>
		</header>
		<p>MAgPIE has so far been used in various scientific publications and
        reports. The following list give some more details about selected 
        applications.</p>
		<ul class="actions">
          <li><a href="#two" class="button next scrolly">Get Started</a></li>
        </ul>
	</div>
</section>


{{site.showcase}}

<!-- Two -->
<section id="two" class="spotlights">
{% for showcase in site.showcases %}
<section>
		<a href="showcase.url" class="image">
			<img src="{% link {{showcase.image}} %}" alt="" data-position="center center" />
		</a>
		<div class="content">
			<div class="inner">
				<header class="major">
					<h3>{{showcase.title}}</h3>
				</header>
				<p>{{showcase.description}}</p>
				<ul class="actions">
					<li><a href="{{showcase.url}}" class="button" target="_blank">Open showcase</a></li>
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
			<h2>Further information</h2>
		</header>
		<p>If you are seeking further information and want to get deeper involved in the MAgPIE and MADRaT development you can have a look at our events page where you can find dates of regular community events as well as the upcoming workshops or related conferences.</p>
		<ul class="actions">
			<li><a href="{% link events.md %}" class="button next">Go to events</a></li>
		</ul>
	</div>
</section>

</div>
