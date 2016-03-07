---
title: Decision Theater North News
layout: default
---

<br />
<h3> DTN in the news </h3>
<br />
{% for post in site.news %}
<p>

<h4><a href="{{ post.link}}">{{post.title}}</a></h4>

{{post.subject}}

</p>

<hr />

{% endfor %}
