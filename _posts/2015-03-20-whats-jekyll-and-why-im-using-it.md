---
layout: post
title: What's Jekyll and why I'm using it
---

[Jekyl](https://jekyllrb.com) is a static site generator, an open-source tool that allows you to build your
own site with the hassle of settings a database server and using an scripted language (like PHP, Ruby, Perl, Python or any 
other you may think of) to run the site. 

Instead Jekyll, like a few others, take a different approach. Why not use a generator engine to lay out all the content from
static files for you? Why don't just concentrate on the content and let the site generator do the build? 

This is what Jekyll does. It builds a directory hierarchy and sets up a set of files for you. Then, it takes that structure
and the files you typed and builds the site for you. 

And because you're just using a bunch of static files with no code at all, means that you don't need to worry about database
security holes, SQL injections or dynamic language security problems. No updates of those pieces of code, you just need to 
have a web server serving static content and you are ready to go. 

Simple, uh? But there's always a drawback. In this case, there are two:

1. You lose the usual WYSWYG editor you used in your previous blog engine
2. You need to find a hosting site for your static files

Instead of having a regular WYSWYG editor, you have to type for files (blog entries, for 
example) using a simple text editor. The real drawback here is that you need to learn a mark up language (a so simple one though) 
to do the typing. If I'm not mistaken, Jekyll ships with two converter, Textile and Markdown. I use the latter and I find this
[cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet#lists) very useful.

About the hosting problem, this was also a problem if you wanted to have your own blog engine without
any restriction from a blog provider (like having your own wordpress instance anywhere). If you don't want to mess with a hosting
provider, DNS setups and such, you may want to use Dropbox's public folder or Google Drive. But if you are a programmer comfortable
with version control tools and coding in the command line, you may find [GitHub pages service](https://pages.github.com/) very useful. 

So these are the reasons why I use it. I find it very useful to just open my **vim** editor, type something in there and just **git-add-push**
everything to github. I'm fine with a domain like **gustauperez.github.io** but I know I can create my own domain anywhere and redirect it
to GitHub. 
