---
author: 7sharp9
date: '2011-05-04 02:18:41'
layout: post
slug: new-projects
status: publish
title: New projects
wordpress_id: '333'
categories:
- Programming Tales
tags:
- asynchronous
- Complexity
- distributed systems
- F#
- flack
- frack
- pipelets
- Pipeline
- SAEA
- Sockets
---

Hello again, since my initial articles on Sockets and pipelines, I have had
quite a bit of interest and I am now pleased to say that these projects have
all grown up and now have a proper home on [GitHub](https://github.com/).

The Sockets and Bockets articles have become
[Flack](https://github.com/7sharp9/flack) and the pipeline articles have
become [Pipelets](https://github.com/7sharp9/pipelets).  Flack is named so
because I always like to use quirky names for libraries and its original name
was a bit controversial, hence Flack i.e. I get a lot of flack about my naming
:)

I have been working on consolidating and expanding the demo code to produce
some useful libraries rather than just proof of concepts and demo's.  I was
contacted pretty early on by Ryan Riley who has been working on
[Frack](https://github.com/panesofglass/frack), for those of you who dont know
Frack is an F# implementation based on Rack:

> Rack provides a minimal, modular and adaptable interface for developing

>     web applications in Ruby.  By wrapping HTTP requests and responses in

>     the simplest way possible, it unifies and distills the API for web

>     servers, web frameworks, and software in between (the so-called

>     middleware) into a single method call.

Ryan wanted to see if the SocketAsyncEventArgs pattern could be used in Frack
to improve efficiency.  So far I have been pushed for time to make a large
amount of progress on integrating the two, but things are progressing faster
now that the library is on [GitHub](https://github.com/).

Please check out [Flack](https://github.com/7sharp9/flack),
[Pipelets](https://github.com/7sharp9/pipelets), and
[Frack](https://github.com/panesofglass/frack), and also Ryan's technical
blog: [Wizards of Smart](http://wizardsofsmart.net/)

The other thing in the pipeline, pun intended, is a distributed pipeline
engine where a pipeline would be exposed via a socket and could be connected
to other pipeline systems.  The beauty of a system like this is you could
compose systems of pipelines to run either on either a single machine or
distributed over a network, this would effectively marry together functional
composition and distributed systems.  Its early days yet but I hope to get
together some more details of how this will work in the coming weeks, Im quite
excited to get working on it!

As always I appreciate any comments or suggestions.

See you soon.

