---
layout: default
title: Home
permalink: /photos
---

### Some of my photos:

{% for gallery in site.data.galleries %}
- [{{ gallery.description }}](galleries/{{ gallery.id }})
{% endfor %}
