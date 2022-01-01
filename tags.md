---
layout: default
title: tags
---
{% assign date_format = site.date_format | default: "%B %-d, %Y" %}

<h1 class="post-title">tag-wise</h1>

<a class="noline" href="{{ './posts' | relative_url }}">[ view list ]</a>

{% assign sorted_cats = site.tags | sort %}

{% for tag in sorted_cats %}
<section id="{{ tag | first }}">
  <h3>{{ tag | first }}</h3>
  <ul>
  {% for post in tag.last %}
    <li><a class="noline mono" href="{{ post.url | relative_url }}">{{ post.title }}</a> - <i>{{ post.date | date: date_format }}</i></li>
  {% endfor %}
  </ul>
</section>
{% endfor %}

<div class="right"> this blog uses <a class="noline" target="_blank" href="https://jekyllrb.com/">jekyll</a></div>
