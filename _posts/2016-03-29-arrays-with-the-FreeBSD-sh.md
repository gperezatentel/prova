---
layout: post
title: Arrays with the default FreeBSD shell
tags: [ FreeBSD, shell scripting ]
---

The default FreeBSD shell is based on the ash shell, which is enough for most tasks. But recently, while
adapting the Xen hotplug scripts I discovered it lacks support for arrays. 

I needed to somehow trick the default shell and implement a kind of array. 

My first thought was to implement a sort of queue using a delimiter and setting the IFS variable to skip that delimiter. That would
have provided me a simple FIFO structure, but it wasn't good enough for what I wanted to accomplish. 

I decided to trick **sh** and 
 use a set of variables whose name contain the index instead. I thought that if I wanted an array named ``lockdict``, why not have a variable named
``lockdict0``, another named ``lockdict1`` and so forth?  

The tricky part comes when want to use a loop variable like ``$i`` to reference a component of the array. By default The shell won't expand the value of
``$i``, and we'll get an error if we try something like this:

{% raw %}
	 _lockdict$i = "test" 
{% endraw %}

To workaround this we are going to need our **eval** friend. If want to assign a value to an index of the 'array', we should do something like this:

{% raw %}
	eval _lockdict$i = "test"
{% endraw %}

If we want to retrieve the assigned value of the ``$i`` component, we should do something like this:

{% raw %}
	eval temp=\$_lockdict$i
{% endraw %} 
