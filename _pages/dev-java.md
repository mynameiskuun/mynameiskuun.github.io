---
title: "Java"
layout: archive
permalink: /dev/java
sidebar:
  nav: "sidebar-category"
---

{% assign posts = site.tags.java %}
{% for post in posts %}
{% include archive-single.html type=page.entries_layout %} {% endfor %}
