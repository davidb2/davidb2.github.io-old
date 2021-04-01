---
layout: page
permalink: /papers/
title: papers
description: all authors are listed in alphabetical order by last name
years: [2020]
nav: true
---

<div class="publications">

{% for y in page.years %}
  <h2 class="year">{{y}}</h2>
  {% bibliography -f papers -q @*[year={{y}}]* %}
{% endfor %}

</div>
