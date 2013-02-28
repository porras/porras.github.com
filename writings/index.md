---
layout: default
title: Sergio Gil · Writings
---

# Writings
## by [Sergio Gil](/)

{% for writing in site.categories.writings %}
* [{{ writing.title }}]({{ writing.url }}){% endfor %}
