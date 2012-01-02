---
author: 7sharp9
date: '2011-09-14 00:08:42'
layout: post
slug: build-2011-a-quick-look
status: publish
title: Build 2011 - A quick look
wordpress_id: '477'
categories:
- Programming Tales
tags:
- .NET 4.5
- Build 2011
- C#
- Development
- F#
- Performance
- Sockets
---

Well, I didn't think I would be doing this but heres some of the sessions Im
looking forward to watching from this years Build conference...

First up is the Server+Cloud section, this section contains the only F#
presentation which is disappointing.

### Server+Cloud

Obviously first up is the F# 3.0 session by Don Syme, really looking forward
to this session!

[**F# 3.0: data, services, Web, cloud, at your
fingertips**](http://channel9.msdn.com/events/BUILD/BUILD2011/SAC-904T)

Modern programming thrives on rich spaces of data, information and services.
With F# 3.0 and Visual Studio 11, you now have a tool that massively
simplifies information-rich analytical programming. F# 3.0 provides integrated
support for F# Information Rich Programming, a new and powerful way of
integrating data and services into your programming experience. In this talk,
we will describe the new features of F# 3.0, including the first released
version of F# Type Providers and F# Queries, with apps to leverage
technologies such as SharePoint, Azure Data Market, OData, Entity Framework
and SQL Server.

  
One of the areas I have a big interest in is performance so the next session
will be interesting.

[**Windows Server performance improvements and
optimisations**](http://channel9.msdn.com/events/BUILD/BUILD2011/SAC-417T)

This session discusses the major performance improvements of Windows Server 8
and demonstrates how customers and partners can achieve their performance and
scalability goals. We will describe some of the ways you can take advantage of
the new capabilities to improve scalability, networking, throughput, and get a
more powerful virtualization experience with Windows Server 8.

  
Network performance is another area I have a big interesting so this session
will be good to see.

**[New techniques to develop low-latency network apps](http://channel9.msdn.com/events/BUILD/BUILD2011/SAC-593T)**  
For performance-critical apps, every microsecond saved means money. Windows
Server 8 makes it possible to increase throughput, handle higher message
rates, reduce latency and jitter, and lower CPU usage, all with standard
server hardware. The new Registered I/O (RIO) model in Windows 8 delivers a
low-latency solution while maintaining on-the-wire compatibility with existing
TCP and UDP protocols. Additionally, a new fast path loopback allows high-
speed apps to achieve a higher level of performance. Attend this session to
learn how to program and tune these new capabilities and to witness them in
action.

  
Parallel programming is a topic that deserves more attention so it will be
good to see what the next in this area.

**[Building parallelized apps with .NET and Visual Studio](http://channel9.msdn.com/events/BUILD/BUILD2011/SAC-808T)**  
The Task Parallel Library (TPL), PLINQ, and Visual Studio 2010 provide managed
code developers with a solid foundation for parallelizing loops, queries, and
other common constructs in both server and client apps. But that’s only the
beginning. In this code-intensive session, learn about what’s next for
parallelization with .NET and Visual Studio, diving deep into performance
enhancements, new Visual Studio tooling, and new libraries and language
support for parallelization and asynchrony.

  
Next up here are the sessions in the Tools section that I will be taking a
look at.

### Tools

I will probably use this session to see if F# will map easily over to the new
Windows Runtime

**[Using the Windows Runtime from C# and Visual Basic](http://channel9.msdn.com/events/BUILD/BUILD2011/TOOL-531T)**  
C#, Visual Basic and the .NET tools have first-class support for the Windows
Runtime. Learn about this integration and how to use C# and Visual Basic to
write Metro style apps that call the Windows Runtime and how to build
libraries that integrate with your Metro style apps using HTML.

  
DirectX isn't really something I spend a lot of time doing but I like to keep
my hand in...

**[A lap around DirectX game development tools](http://channel9.msdn.com/events/BUILD/BUILD2011/TOOL-761T)**  
Visual Studio 11 brings the most significant set of improvements for
developing graphics-intensive apps in over a decade. Whether you are just
getting started with 2D/3D games or a self-proclaimed "guru," there's
something for you in this talk. We will walkthrough a slew of new tools
integrated into Visual Studio that will make your life better.

  
I dont tend to spend as much time in the debugger with F# but when I do this
information will be good to know.

[**Advanced IntelliTrace in production with Visual Studio
11**](http://channel9.msdn.com/events/BUILD/BUILD2011/TOOL-792T)

Did you ever have that bug that you just could not reproduce? Not getting
enough information from production environments to really solve your problems
quickly? Why does it always take way longer to figure out what the problem is
then to make the fix? In this session, see how IntelliTrace, within Visual
Studio 11, gives you the ability retrieve rich, detailed information about
problems, even in production, enabling you to spend less time on bug
diagnostics and more time writing software.

  
Again this section will be good to see just to find out if this can be ported
over to F#

[**Taming GPU compute with C++
AMP**](http://channel9.msdn.com/events/BUILD/BUILD2011/TOOL-802T)

Developers today inject parallelism into their compute-intensive apps in order
to take advantage of multi-core CPU hardware. Beyond CPUs, however, compute
accelerators such as general-purpose GPUs can provide orders of magnitude
speed-ups for data parallel algorithms. How can you as a C++ developer fully
utilize this heterogeneous hardware from your Visual Studio environment? How
can you benefit from this tremendous performance boost in your Visual C++
solutions without sacrificing developer productivity? The answers will be
presented in this session introducing C++ AMP.

  
Delving into the depths of stuff is always interesting so this will be great.

[**Deep dive into the kernel of the .NET
Framework**](http://channel9.msdn.com/events/BUILD/BUILD2011/TOOL-813T)

The Common Language Runtime is the cutting-edge virtual machine at the heart
of the .NET Framework. In this session, we'll dive deep under the covers of
the CLR and discuss some of our key innovations for .NET Framework 4.5 and
Windows 8. Topics will include updates in the code-generation and diagnostics
space, improvements in our garbage collector for low latency server scenarios,
and automatic NGen.

  
It will be good to keep up with the latest developments in the C# world, no
doubt the new async features are influenced by F# and other functional
programming concepts.

[**Future directions for C# and Visual
Basic**](http://channel9.msdn.com/events/BUILD/BUILD2011/TOOL-816T)

In this talk, Technical Fellow Anders Hejlsberg will share project plans for
the future directions of C# and Visual Basic, including a discussion of what
trends are influencing and shaping the direction of programming languages.
Anders will talk about asynchronous programming and Windows 8 programming,
coming in the next version of Visual Studio. He will also discuss the long-
lead project “Roslyn”, including object models for code generation, analysis,
and refactoring, and upcoming support for scripting and interactive use of C#
and Visual Basic.

  
New features of Visual Studio are always good to know.

**[What's new in Visual Studio 11](http://channel9.msdn.com/events/BUILD/BUILD2011/TOOL-820F)**  
Microsoft Visual Studio 11 enable developers to take full advantage of the
capability of Windows using the skills and technologies developers already
know and love to deliver exceptional and compelling apps. Whether working
individually or in a small, medium or large development team the Visual Studio
11 sets a new standard for development tools, helping teams deliver superior
results for their customers that help set them apart from their competitors.
In this session we’ll walk through the new features in the Visual Studio 11
Developer Preview to give you an understanding of the breadth of tooling
available in this release.

  
Looking forward to hearing about the new .NET 4.5 features.

[**What's new in .NET Framework
4.5**](http://channel9.msdn.com/events/BUILD/BUILD2011/TOOL-834T)

The next major release of the .NET Framework, .NET 4.5, allows you to easily
use Windows 8 technologies, like Windows Runtime, directly from .NET 4.5.
Accessing your data is easier than ever with support for the newest features
in SQL Server and support for WebSockets. Programs are more responsive, with
the AWAIT keyword, faster ASP.NET startup and an improved server Garbage
Collector. .NET 4.5 incorporates key customer feedback, with the newest MEF
features, support for long running workflows with State Machines, and improved
HTML 5 support in ASP.NET. In this overview talk, you’ll learn about all of
these technologies, and get pointers to deeper dives where you can learn more.

  
Finally heres the session in the platform section that I find interesting.

### Platform

Socket based programming is an interesting area at least it is for me so this
all be an interesting session.

[**Building Windows runtime sockets
apps**](http://channel9.msdn.com/events/BUILD/BUILD2011/PLAT-580T)

Today's networks have grown more complicated as a result of multihoming, web
proxies, security issues, internationalization and other issues. Because
dealing with these complexities is hard, it is either ignored or significant
resources are spent attempting to do so. In this session, we will demonstrate
how the Windows Runtime sockets API simplifies what an app must do to use TCP
or UDP. We also will present exciting new functionality, such as proximity
discovery and using WebSockets for HTTP proxy traversal, as well as how to
handle complex security and cost issues.

  
Looking throughout the more client based areas in the platform section the
next two might be worth a look.

[**Achieving high performance 2D graphics with
Direct2D**](http://channel9.msdn.com/events/BUILD/BUILD2011/PLAT-769T)

Make it fast! Great performance is a huge motivator of satisfaction and user
preference with apps. Direct 2D powers high-performance 2D graphics rendering
in Windows 8. In this session, you will learn advanced techniques for
optimizing your Direct2D code for maximum speed and efficiency in your apps.

[**Create cool image effects with
Direct2D**](http://channel9.msdn.com/events/BUILD/BUILD2011/PLAT-770T)

Every day, people are using apps that process images to create a variety of
effects. The new image processing infrastructure in Windows 8 enables apps to
perform high-performance image enhancements, transformation and composition
using the GPU for photos, vector graphics and UI elements. In this session,
you will learn about the Direct2D effect API, and see how easy it is to apply
effects to photos and 2D and 3D content in your Windows 8 app to deliver more
polished visual experiences to your customers.

  
I had a look through the Apps and Hardware sections but there were not any
specific areas in there that I found really interesting.

Wow this post is a bit longer than I wanted it to be but all in all there
looks to be some very interesting sessions.  There is a lot of Metro based
information but Ill be keeping a bit of a distance from that stuff to
concentrate on the server-side material for now.

Until next time...

