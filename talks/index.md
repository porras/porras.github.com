---
layout: default
title: Sergio Gil · Talks
---

# Talks
## by [Sergio Gil](/)

In this page I put together the materials from different talks I've done.

{% for talk in site.categories.talks %}
* [{{ talk.title }}]({{ talk.url }}) ({{ talk.date | date: "%B %Y" }}){% endfor %}
