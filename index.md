---
layout: page
title: scriptcs
tagline: Official team blog
---
{% include JB/setup %}

{% for post in site.posts limit 4 %}
<div class="date"><small>{{ post.date | date_to_string }} </small></div>
<h2><a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a><small> - {{ post.tagline }}</small></h2>
{{ post.content }}
<hr />
{% endfor %}
<a href="/archive.html">More posts...</a>
