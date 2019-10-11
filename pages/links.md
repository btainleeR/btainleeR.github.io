---
layout: page
title: Links
description: 没有链接的博客是孤独的
keywords: 友情链接
comments: true
menu: 链接
permalink: /links/
---

> 一些好朋友的链接.

{% for link in site.data.links %}
  {% if link.src == 'friends' %}
* [{{ link.name }}]({{ link.url }})
  {% endif %}
{% endfor %}

> 交换链接

{% for link in site.data.links %}
  {% if link.src == 'exchange' %}
* [{{ link.name }}]({{ link.url }})
  {% endif %}
{% endfor %}
