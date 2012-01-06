---
author: 7sharp9
date: '2011-01-09 20:29:00'
layout: post
slug: introduction
status: publish
title: Introduction
wordpress_id: '34'
comments: true
categories:
- Programming Tales
tags:
- C#
- Development
- F#
- Multithreading
- Performance
---

Let me spend a couple of minutes explaining what Im going to be posting about,
heres a little bit of background...

First of all I'm a seasoned C# programmer, I develop enterprise business
applications, mainly specialising in backend architectures, multithreading and
generally poking about in the guts of things trying to wrangle out the last
few drops of performance!
  
At the beginning of last year I decided to catch up with some of the
technologies that have reared their heads while I wasn't looking, so I set
about doing more research into Silverlight,WPF, and F#. Silverlight and WPF,
and really enjoyed getting to grips with XAML and embracing the separation
between UI and logic, but for me the real interesting thing was learning about
F#.<!-- more -->

I have recently been reading [Professional F# 2.0](http://www.amazon.com/gp/product/047052801X?ie=UTF8&tag=blacguitandge-20&linkCode=as2&camp=1789&creative=9325&creativeASIN=047052801X) 
by Wrox press, I think Chapter 1 sums things up pretty well on why F# is so interesting, heres a snippet that caught my eye:

>Unfortunately, despite the huge success of C++, Java, and .NET, the
original premise of the object oriented paradigm, that developers could take
objects ‚"off the shelf", and reuse them without modification, has yet to
occur for anything but the infrastructural domain. Even in that domain, debate
and duplication rages over subtle points of design that can only be changed by
changing the underlying source code making up those infrastructural types.  

then a couple of paragraphs later:

> Then, subtly, a new problem emerged: the underlying hardware ceased its
steady march of improved performance and instead began to respond to the beat
of a different drum, that of multi- core. Suddenly, where programmers used to
face problems in a single or slightly multi-threaded environment, now the
hardware demanded that additional performance would come only from greater and
greater parallelization of code. Where the object-oriented developers of the
20th century could assume that only a single logical thread of execution
would operate against their objects, the developers of the 21st century
has to assume that many logical threads of execution will all be hammering on
their objects simultaneously. The implication, that developers must now
consider all possible interactions of multiple threads on every operation
available on the types they define, continues to hang like a proverbial Sword
of Damocles over the heads of object-oriented programmers even as the first
decade of the new century came to a close.  

Multithreaded development in an enterprise environment can be very challenging
affair and any tools that can give me an advantage Im not going to ignore.

In essence Im going to be posting about C# and F#, and embracing the multicore
revolution thats knocking at all our doors, interesting uses of F#,
integrating with other frameworks like silverlight, WPF and WCF and general
programming goodness!
