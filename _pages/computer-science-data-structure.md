---
title: "Data Structure"
layout: archive
permalink: /computer-science/data
sidebar:
  nav: "sidebar-category"
---

{% assign posts = site.tags.data %}
{% for post in posts %}
{% include archive-single.html type=page.entries_layout %} {% endfor %}
