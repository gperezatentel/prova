---
layout: post
title: Listing tags and categories with Jekyll
tags: [ jekyll ]
---

Since Jekyll supports `site.tags` and `site.categories` it would very simple to 
have a page to list all the tags and which posts do contain that tag. 

I've created a new page  `tags/index.html` using the default layout and using 
some code to list the tags:

{% raw %}

    ---
    layout: default
    title: Tags
    ---

    {% for tag in site.tags %}
      {% assign t = tag | first %}
      {% assign posts = tag | last %}

    {{ t | downcase }}
    <ul>
    {% for post in posts %}
      {% if post.tags contains t %}
      <li>
        <a href="{{ post.url }}">{{ post.title }}</a>
        <span class="date">{{ post.date | date: "%B %-d, %Y"  }}</span>
      </li>
      {% endif %}
    {% endfor %}
    </ul>
    {% endfor %}

{% endraw %}
