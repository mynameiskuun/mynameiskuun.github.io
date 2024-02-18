---
title: "DEV"
layout: archive
permalink: /dev/all
sidebar:
  nav: "sidebar-category"
---

{% assign posts = site.categories.dev %}
{% for post in posts %}
{% include archive-single.html type=page.entries_layout %} {% endfor %}
