---
# vim:ft=liquid
# liquid_subtype=pandoc
layout: 'index'
title: 'Jake Zimmerman'
subtitle: 'A collection of my writings about programming'
# Always regenerate, even with --incremental
regenerate: true
---

Here I write technical posts across a broad category of topics, mostly related
to programming languages and productivity tips.

→ 👨‍💻 [More about me](https://jez.io)\
→ 📺 [Talks I've given](https://jez.io/talks/)\
→ 💭 [Thoughts and opinion pieces](https://jez.io/thoughts/)

{% capture numposts %}{{ site.posts | size }}{% endcapture %}
{% if numposts != '0' %}

# Suggested posts

{% assign pinned_posts = site.posts | where: "pinned", true %}
{% for post in pinned_posts -%}
## {{ post.title }}

> {{ post.date | date: '%B %-d, %Y' }}

{{ post.description }}

_[Read more →]({{ post.url }})_

{% endfor %}

# All posts

→ [Posts by category](categories/)

{% for post in site.posts %}{% assign currentyear = post.date | date: "%Y" %}{% if currentyear != prevyear %}

## {{ currentyear }}

{% assign prevyear = currentyear %}{% endif %} - [{{ post.title }}]({{ site.baseurl }}{{ post.url }}), {{ post.date | date: '%B %-d' }}
{% endfor %}
{% else %}

# All posts

- *None to show.*
{% endif %}

<p class="signoff">
  [↑ Back to top](#posts)
</p>
