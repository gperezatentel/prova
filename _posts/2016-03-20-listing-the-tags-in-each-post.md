---
layout: post
title: Listing tags in each post
tags: [ Jekyll ]
---

To list the tags of each post, we have to change `index.html` and the post layout. Changing 
the `index.html` is needed because (at least for me) the build process when
browsing all the posts is different than the one used when viewing a single post. 

In the first case, I needed to add this; be sure there's **post** variable defined when using this piece of code or the parse would fail) to `index.html`:

{% raw %}
     tags: {% for tags in post.tags %} {{ tags }} {% endfor %}
{% endraw %}

In the second one, I needed this snipped of code:

{% raw %}
    
    tags: {% for tags in page.tags %} {{ tags }} {% endfor %} 

{% endraw %}
