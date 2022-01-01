---
layout: default
title: de[v]blog
---

<h2 class="mono">hi, i am taylor, kim.</h2>
I love programming and decorating my desktop environment.

### what am i ?
* linux desktop lover
* fullstack developer
* content creator
{: class="mono"}

view <a class="noline" href="{{ '/about' | relative_url }}">[about me]</a>

## recent posts:

<ul class="mono">
{% for post in site.posts limit:5 %}
	{% if post.category == 'random' %}
		<li><a class="noline" href="{{ post.url | relative_url }}">[r] {{ post.title | downcase }}</a></li>
	{% else %}
		<li><a class="noline" href="{{ post.url | relative_url }}">{{ post.title | downcase }}</a></li>
	{% endif %}
	
{% endfor %}
<a class="noline" href="{{ '/posts' | relative_url }}">see more..</a>
</ul>
