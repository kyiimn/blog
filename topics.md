---
layout: default
title: topics
---
{% assign date_format = site.date_format | default: "%B %-d, %Y" %}

<h2>blog posts: topic-wise </h2>

<a class="noline" href="{{ './posts' | relative_url }}">[ view list ]</a>

{% assign sorted_cats = site.categories | sort %}

{% for category in sorted_cats %}
<section id="{{ tag | first }}">
  <h3>{{ category | first }}</h3>
  <ul>
  {% for post in category.last %}
    <li><a class="noline mono" href="{{ post.url | relative_url }}">{{ post.title }}</a> - <i>{{ post.date | date: date_format }}</i></li>
  {% endfor %}
  </ul>
</section>
{% endfor %}

<div class="right"> this blog uses <a class="noline" target="_blank" href="https://jekyllrb.com/">jekyll</a></div>