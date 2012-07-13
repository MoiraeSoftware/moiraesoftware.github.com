---
author: 7sharp9
date: 2012-07-13 23:13
layout: post
slug: The-Lurking-Horror
status: publish
title: "The Lurking Horror"
comments: true
categories: [Agents, Programming Tales]
tags: [Async Workflows, asynchronous, F#, Agents, MailboxProcessor]
---
{% blockquote%}Deep in the darkest depths lurks an ancient horror, when the time is right it will rise forth and leave you screaming for mercy and begging for forgiveness...{%endblockquote%}
 
OK, I have a penchant for being over dramatic but in this post I am going to reveal some little known caveats in a well known and much revelled area of F#, agents aka the `MailboxProcessor` gasp!
 
First let me give you a demonstration:
 
{% codeblock lang:fsharp %}
open System
open System.Diagnostics
type internal BadAgentMessage =
  | Message of string * int
  | Lock
  | Unlock
   
type BadAgent() =
  
  let agent = MailboxProcessor.Start(fun agent ->
    let sw = Stopwatch()
    let rec waiting () =
      agent.Scan(function
        | Unlock -> Some(working ())
        | _ -> None)
 
    and working() = async {
      let! msg = agent.Receive()
      match msg with
      | Lock ->   return! waiting()
      | Unlock -> return! working()
      | Message (msg, iter) ->
          if iter = 0 then sw.Start()
          if iter % 10000 = 0
            then sw.Stop()
                 printfn "%s : %i in: %fms" msg iter sw.Elapsed.TotalMilliseconds
                 sw.Restart()
          return! working() }
    working())      
 
  member x.Msg(msg) = agent.Post(Message msg)
  member x.Lock() = agent.Post(Lock)
  member x.Unlock() = agent.Post(Unlock)
{% endcodeblock %}

The `BadAgentMessage` type defines a discriminated union that we are going to use for the agents message interface.  This is comprised of three elements:  

* **Message:**  This will just be a simple string based message and an int uses as a counter.  
* **Lock:**  This is used to stop message processing within the agent by causing it to wait for an `Unlock` message to arrive.  
* **Unlock:**  This message is used to resume the processing within the agent, effectively exiting the locked state.   

We have two main sections to the agents body which I will describe below.

###working
The purpose of the `working` function is to dequeue the messages from the agent and process them with pattern matching; `let! msg = agent.Receive()` is used to get the next message which is then pattern matched to be one of the three messages types of the `BadAgentMessage`.  When the `Lock` message is encountered `return! waiting()` is used to place the agent in a state where it is waiting for an `Unlock` message to arrive.  An `Unlock` message simply resumes processing by calling `return! working()`.  The only real purpose of the `Unlock` message is to exit from the locked state that is introduced by the `Lock` message.  The `Message` message simply starts a `StopWatch` on the first operation by using the Messages counter, and then stops it again on the 10,000th operation.  At this point the time taken is also printed to the console and the `StopWatch` is restarted before resuming the main processing loop by calling `return! working()`

###waiting
This function is simply using the agents scan function to wait for an `Unlock` message to arrive, once it does it puts the agent back into normal operation by calling the `working()` function.  If the message does not match an `Unlock` message then `None` is returned and the agent simple waits for the next message before trying again.  

The rest of the agent is just ancillary member functions to allow easy sending of the three message types.  
 
###Test Harness
And here's a very simple test harness:
 
{% codeblock lang:fsharp %}
let ba = BadAgent()
 
printfn "Press and key to start"
Console.ReadLine() |> ignore
let dump number =
    for i in 0 .. number do
        ba.Msg("A message", i)
 
ta.Lock()
dump 200000
ta.Unlock()
 
Console.ReadLine() |> ignore
{% endcodeblock %}
 
OK, so this is a very synthetic test but I just wanted to highlight some of the internal behaviour.  If I run this code I get the following console output:
 
{% img https://lh5.googleusercontent.com/-chMoEOya7CE/T_tRraiW_eI/AAAAAAAABbY/wsQkWbm4DJM/s677/ConsoleTimes.png %}
 
You can see that the time to process the first 10,000 messages is 3083ms then it steadily decreases until the last 10,000 messages are processed in 94ms.  So the processing time for 10,000 messages is about 32 times slower at the beginning than as it is at the end.
 
##Opening it up
Let's take a look at some of the internals of the `MailboxProcessor` to understand what's going on. First of all the core functionality is contained within the `Mailbox` type, with the MailboxProcessot acting as an augmenter adding functionality to do the async reply for: TryPostAndReply, PostAndReply, PostAndTryAsyncReply, and PostAndAsyncReply.  the extra functionality
added here revolves around the use of `ResultCell` and `AsyncReplyChannel` to control the synchronous and asynchronous results.  You might want to refer to my earlier series which describes implementing the MailboxProcessor with TPL Dataflow [Part 1][], [Part 2][], [Part 3][]
 
{% codeblock lang:fsharp%}
type Mailbox<'Msg>() = 
    let mutable inboxStore  = null
    let mutable arrivals = new Queue<'Msg>()
{% endcodeblock %}
 
Heres the initial type definition for the Mailbox, you can see that  there are two mutable fields:
 
`inboxStore` Is a generic List type (System.Collection.Generic.List<T>) and `arrivals` is a `System.Collections.Generic.Queue<T>` type. 
 
For now the inboxStore is null and is only ever assigned via `Scan` or `TryScan` and this is indirectly via the `x.inbox` member shown here:
 
{% codeblock lang:fsharp %}
member x.inbox =
    match inboxStore with
    | null -> inboxStore <- new System.Collections.Generic.List<'Msg>(1) // ResizeArray
    | _ -> ()
    inboxStore
{% endcodeblock %}
 
The code in the Mailbox can quickly get out of hand if you try to keep all the function calls in your head at once, Ill outline
the process as a series of sections below.

##Scan 
First up there is `Scan` this member function is just an async wrapper around `TryScan` if `TryScan` returns None an exception is
raised, if not then the result is returned from `TryScan`.
 
So lets look at `TryScan`. 
 
{% codeblock lang:fsharp %}
member x.TryScan ((f: 'Msg -> (Async<'T>) option), timeout) : Async<'T option> =
    let rec scan() =
        async { match x.scanArrivals(f) with
                | None -> // Deschedule and wait for a message. When it comes, rescan the arrivals
                          let! ok = waitOne(timeout)
                          if ok then return! scan() else return None
                | Some resP -> let! res = resP
                               return Some(res) }
    // Look in the inbox first
    async { match x.scanInbox(f,0) with
            | None  -> return! scan()
            | Some resP -> let! res = resP
                           return Some(res) }
{% endcodeblock %}
 
You can see here that an async workflow is declared that first pattern matches on `x.scanInbox`, passing in the predicate scan function `f` and 0.  If `None` is returned then there is no match and the recursive function `scan` is returned.  This time the function `x.scanArrivals` is be called, again passing in the predicate function `f`.  

* An interesting point to note is that each message that arrives that doesn't match the predicate `f` resets the  timer `let! ok = waitOne(timeout)`, that means that any number of trivial messages that arrive keep the `x.TryScan` function running.  This was also  mentioned by Jon Harrop in a Stackoverflow question entitled [How to use TryScan in F# properly][Stackoverflow].  Jon also mentions locking which I will address in the scanArrivals section below.  
 
So what's the difference between `scanArrivals` and `scanInbox`?   
 
`scanInbox` operates on the `inboxStore` which you might remember is a `List<T>` type and `scanArrivals` operates on the `arrivals` which is the `Queue<T>` type.  The big difference between these two is that messages that first arrive in the Mailbox get sent to the arrivals queue first and when messages are not matched by the predicate function `f` they are added to the `inboxStore` hence the need to always check the `inboxStore` before the `arrivals` queue otherwise previously unmatched scan messages would not be processed correctly.  

##scanArrivals / scanArrivalsUnsafe
Lets look at the `x.scanArrivals` function, it's just a lock construct around the `x.scanArrivals` function.  This leads to an important point; the scan function is operating under a lock, effectively what this means is that user code is executed under the lock and you never know what is going to happen in the user code if they hold onto the scan operation for any length of time then there will be significant blocking at the normal receive mechanism into the queue will be backed up due to the lock which also uses the same lock `syncRoot` when receiving.  
     
{% codeblock lang:fsharp %}
member x.scanArrivalsUnsafe(f) =
    if arrivals.Count = 0 then None
    else let msg = arrivals.Dequeue()
         match f msg with
         | None ->
             x.inbox.Add(msg);
             x.scanArrivalsUnsafe(f)
         | res -> res
 
// Lock the arrivals queue while we scan that
member x.scanArrivals(f) = lock syncRoot (fun () -> x.scanArrivalsUnsafe(f))
{% endcodeblock %}
 
If you read the `MailBoxProcessor` documentation on [MSDN][MsdnMbp]: 
{% blockquote %}For each agent, at most one concurrent reader may be active, so no more than one concurrent call to Receive, TryReceive, Scan or TryScan may be active.{% endblockquote %}

##scanInbox
A quick glance at `x.scanInbox` reveals another function where there could be heavy weight performance implications.   The `inbox` is a `List<T>` type and the `RemoveAt` function does an internal `Array.Copy` for each removal.  This is an O(n) operation where n is (Count - index), so as soon as the list gets to a reasonable size then this then is going to really start chewing into your processing time. 

{% codeblock lang:fsharp %}
member x.scanInbox(f,n) =
    match inboxStore with
    | null -> None
    | inbox ->
        if n >= inbox.Count
        then None
        else
            let msg = inbox.[n]
            match f msg with
            | None -> x.scanInbox (f,n+1)
            | res -> inbox.RemoveAt(n); res
{% endcodeblock %}
 
Just to check this theory out lets look at a profile run of the console test that we showed earlier:
 
{% img /uploads/profile_run.png %}
 
Yeah there it is, a whopping 44.41% of the time is spent in Remove at.  Also notice that there were 200,000 calls which mirrors the number we placed in the queue before using the Lock/Unlock message types.
 
One of the things that really stands out for me is that the `inbox` is a simple list and completely unbounded.  In a high throughput situation where a scan operation is being used it's perfectly feasible to get into a runaway memory and CPU condition, where the unmatched messages are sitting in the `inbox` taking longer and longer to processes due to the O(n) operation that takes place on the `RemoveAt` function.  

##Conclusion
So what conclusion can we draw from all of this?  

* Firstly be careful with usage of `Scan` and `TryScan`, in certain situations the internal queue could back up to a certain size where you will be constantly struggling against the O(n) operation cost.  
* Agents are not a silver bullet solution, they cannot solve every problem.  Although its possible to use agent based techniques to solve various problems like blocking collections and such like, you have to use care and diligence in the solution to avoid introducing another problems into the mix.  I have seen several implementations that I have been able to break relatively easily.  
* Do I still use agents?  **Absolutely!**  Agents are a fabulous tool to have in our toolbox and some extremely elegant solution exist to solve very complex problems. 
* Do I use `Scan` or `TryScan`?  Not in its current form in the `MailboxProcessor`.  I chose to implement a destructive scan in my [TDF agent][Part 3] for the reasons discussed here.  

Actually, now that I look back on that post I don't think I fully described that part effectively as this post was supposed to cover it.  Lets quickly finish of with that.  

####Destructive TryScan
{% codeblock lang:fsharp %}
member x.TryScan((scanner: 'Msg -> Async<_> option), timeout): Async<_ option> =
    let ts = TimeSpan.FromMilliseconds(float timeout)
    let rec loopForMsg = async {
        let! msg = Async.AwaitTask <| incomingMessages.ReceiveAsync(ts)
                                      .ContinueWith(fun (tt:Task<_>) ->
                                          if tt.IsCanceled || tt.IsFaulted then None
                                          else Some tt.Result)
        match msg with
        | Some m ->  let res = scanner m
                     match res with
                     | None -> return! loopForMsg
                     | Some res -> return! res
        | None -> return None}
    loopForMsg
{% endcodeblock %}

A message is dequeued on the line 4 with `let! msg = Async.AwaitTask ...`.  This is then processed by the pattern matching expression on line 9 `| Some m ->  let res = scanner m`.  If the result of the scanner function results in `None` being returned then the message is discarded and the next operation continues with another call to `loopForMsg`, otherwise the message is returned with `| Some res -> return! res`.

One of the areas where I have a lot of experience is using pipelined operations based on input from network I/O.  One of the things that always causes a problem is unbounded situations such as having a queue with no absolute limit.  There comes a time when you have to protect yourself from what is effective a denial of service, you have to either destructively terminate messages or connections or route the overflowed data for processing later.  

OK I'm starting to digress now, so I'll see you all next time...

[MsdnMbp]: http://msdn.microsoft.com/en-us/library/ee353583.aspx "MSDN: F# MailbocProcessor"
[Stackoverflow]: http://stackoverflow.com/a/4891920/607275 "How to use TryScan in F# properly"
[Part 1]: /blog/2012/01/22/FSharp-Dataflow-agents-I/ "FSharp Dataflow agents - Part 1"
[Part 2]: /blog/2012/01/30/FSharp-Dataflow-agents-II/ "FSharp Dataflow agents - Part 2"
[Part 3]: /blog/2012/02/20/fsharp-dataflow-agents-III/ "FSharp Dataflow agents - Part 3"
[UDP]: http://en.wikipedia.org/wiki/User_Datagram_Protocol "wikipedia Udp protocol"