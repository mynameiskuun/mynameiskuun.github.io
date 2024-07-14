---
title: "이펙티브 자바"
layout: archive
permalink: /book-study/effective-java
sidebar:
  nav: "sidebar-category"
---

{% assign posts = site.tags.effective-java %}
{% for post in posts %}
{% include archive-single.html type=page.entries_layout %} {% endfor %}
