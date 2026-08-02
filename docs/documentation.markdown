---
layout: default
title: Documentation
nav_order: 2
has_children: true
permalink: /documentation/
---

# Documentation

Browse the guides below to learn how to use Ardulink-2, from basic setup to advanced integrations.

## Guides

| Guide | Description |
|:------|:------------|
{%- assign guides = site.posts | where: "parent", "Documentation" | sort: "nav_order" -%}
{%- for guide in guides %}
| [{{ guide.title }}]({{ guide.url | relative_url }}) | {{ guide.description }} |
{%- endfor %}
