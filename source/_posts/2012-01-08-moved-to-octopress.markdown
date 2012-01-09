---
author: 7sharp9
layout: post
title: Moved To Octopress...
slug: moved_to_octopress
date: 2012-01-08 22:30
comments: true
categories:
- Programming Tales
tags:
- Blog
- Wordpress
- Octopress
---
{% img left http://octopress.org/images/logo.png 150 %}Well I finally did it, I moved my blog from [Wordpress](http://wordpress.com/) to [Octopress](http://octopress.org/), what drove me to make this move?  

Well, there were a few factors involved...<!-- more -->

I found the following problems and frustrations with Wordpress:

* Changing a Wordpress theme is somewhat of a black art.
* My blog didn't look its best on mobile devices, there was no fluid layout.
* Wordpress seems to introduce a fair bit of latency for somethings thats essentially a simple task 
i.e. get some text from a database.
* Reliance on a database.  
All my post were in a MySQL database although easily maintained I had to have it hosted somewhere.
* Work press editing is both buggy and frustrating.  
I used to get near the end of a complex post only to find that save draft crashed the editor and I lost all my changes!
* Embedding code into a blog involved a Wordpress plugin, a Visual Studio plugin and manual manipulation of Html.

So what do I get from Octopress?

In a single word:
###Simplicity

* No database or hosting is is needed, the blog is statically generated and all hosted on [Github](https://github.com/).
* I don't need to use a clunky web based editor.  I can use a simple text editor as all post are written in .markdown format.
* I get a nice simple elegant blog:
    * A Semantic Html 5 template.
    * A Mobile first responsive layout.
    * Built in 3rd party support for Twitter, Google Plus One, Disqus Comments, Pinboard, Delicious, and Google Analytics.
    * An easy deployment strategy using Github pages or Rsync.
    * Built in support for POW and Rack servers.
    * Easy theming with Compass and Sass.
    * A Beautiful Solarized syntax highlighting.
	
So here here I am with my first post, I'm really pleased so far.  If you want to find out more details on
 how this all works checkout [Octopress](http://octopress.org/).

Until next time.