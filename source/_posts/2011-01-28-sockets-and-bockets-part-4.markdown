---
author: 7sharp9
date: '2011-01-28 14:31:55'
layout: post
slug: sockets-and-bockets-part-4
status: publish
title: Sockets and Bockets 4
wordpress_id: '180'
categories:
- Programming Tales
tags:
- F#
- GC
- Memory fragmentation
- Multithreading
- Performance
- SAEA
- Sockets
- YourKit
---

### Welcome to part 4

[Part 1](http://moiraesoftware.com/?p=39) [Part
2](http://moiraesoftware.com/?p=68) [Part 3](http://moiraesoftware.com/?p=139)
Part 4

If you were looking forward to some exciting new F# code this time your going
to be disappointed, however if you are like me and like looking at graphs and
stats and digging in deeper into the code then your going to enjoy this, lets
get started...

I set up a 5 minute test with 50 clients connecting to the server with a 15ms
interval between each one.  Once connected each client receives a 128 byte
message from the server every 100ms so this will be a 500 messages per second
test.  I am going to be using an excellent product called [YourKit Profiler
for .NET](http://bit.ly/e4ToaO ) it can do both memory and CPU profiling as
well as displaying telemetry for things like thread count, stack contents,
memory allocations etc.  It can be configured to be a lot less intrusive than
a lot of other profilers and I have had a lot of success using it.  You can
download a demo from their site using the link above.  I will be doing some
other articles on using profiling and analysis tools later on so stay tuned
for those too.  All of the graphs and information gathered in this post come
from YourKits output during CPU and memory profiling.

Before we start here's a reminder of what the client code looks like, this is
a simple test client using Brian's code as mentioned in
[Part1](http://moiraesoftware.com/?p=39) I have highlighted the lines that
have changed below.

    
    open System.Net
    open System.Net.Sockets  
    let quoteSize = 128  
    type System.Net.Sockets.TcpClient with
      member client.AsyncConnect(server, port, clientIndex) =
        Async.FromBeginEnd(server, port,(client.BeginConnect : IPAddress * int * _ * _ -> _), client.EndConnect)  
    let clientRequestQuoteStream (clientIndex, server, port:int) =
      async {
        let client = new System.Net.Sockets.TcpClient()
        do!  client.AsyncConnect(server,port, clientIndex)
        let stream = client.GetStream()
        let! header = stream.AsyncRead 1 // read header
        while true do
          let! bytes = stream.AsyncRead quoteSize
          if Array.length bytes <> quoteSize then
            printfn "client incorrect checksum"
      }  
    let myLock = new obj()  
    let clientAsync clientIndex =
      async {
        do! Async.Sleep(clientIndex*15)
        if clientIndex % 10 = 0 then
          lock myLock (fun() -> printfn "%d clients..." clientIndex)
        try
          do! clientRequestQuoteStream (clientIndex, IPAddress.Loopback, 10003)
        with e ->
          printfn "CLIENT %d ERROR: %A" clientIndex e
          //raise e
      }  
    Async.Parallel [ for i in 1 .. 50 -> clientAsync i ]
      |> Async.Ignore
      |> Async.Start
    System.Console.ReadKey() |> ignore

### CPU and threading performance

First of all lets look at the CPU results from the IAsync pattern:

![](http://moiraesoftware.com/wp-content/uploads/2011/01/mcnamara-cpu1.png)

Heres the same run from the SAEA pattern:

![](http://moiraesoftware.com/wp-content/uploads/2011/01/Bocket-cpu2.png)

You can see that both the number of threads and the amount of CPU is a quite a
lot less in the SAEA pattern.  The spike at the beginning is the allocation of
buffers for the BocketPool.

Now lets move on to memory and garbage collection.

### Memory Allocation

Here's a graph of the heap and process memory allocation in the IAsync
pattern, green is generation 0, blue is generation 1 and orange is the large
object heap.  There's also red for generation 2 but the results are behind the
others and they are only small 0,2 MB peaks at 5 to 15 second intervals.

[![](http://moiraesoftware.com/wp-content/uploads/2011/01/mcnamara-
mem1.png)](http://moiraesoftware.com/wp-content/uploads/2011/01/mcnamara-
mem1.png)

Heres the same but for the SAEA pattern, there are red peaks every 10- 20
second intervals of 0.2MB hidden under the others.

[![](http://moiraesoftware.com/wp-content/uploads/2011/01/Bocket-
mem1.png)](http://moiraesoftware.com/wp-content/uploads/2011/01/Bocket-
mem1.png)

As you can see the heap memory is around half the size and the process memory
is 15MB less.

### Memory Hotspots

Finally here's a couple of screen shot of the hot spots for memory allocations
in both implementations

[caption id="attachment_201" align="alignnone" width="300" caption="IAsync hot
spots"][![](http://moiraesoftware.com/wp-content/uploads/2011/01/IAsync-hot-
300x209.png)](http://moiraesoftware.com/wp-content/uploads/2011/01/IAsync-
hot.png)[/caption]

[caption id="attachment_202" align="alignnone" width="300" caption="SAEA
hotspots"][![](http://moiraesoftware.com/wp-content/uploads/2011/01/SAEA-hot-
300x209.png)](http://moiraesoftware.com/wp-content/uploads/2011/01/SAEA-
hot.png)[/caption]

You can clearly the IAsync allocations are not present in the SAEA
implementation and there are 310,188 of them, that's 27% of the total garbage!

### Final thoughts

The SAEA pattern definitely cuts down on memory and CPU usage, yes it adds a
lot of complexity but if your application is dealing with a very high volume
of traffic or clients and you need optimal performance then I think its the
way to go.

The optimisations don't stop there either, if you think about it the receive
Bocketpool is not even used here, if we collapsed all of the BocketPools into
a single contiguous store then we would use even less resources, this means we
could support even more clients or throughput.  In a typical high volume
scenario you are looking at doubling your throughput or number of client
connections.

There's definitely a lot more or interesting things to explore in this area.

As usual any comments are welcome.

See you next time...

