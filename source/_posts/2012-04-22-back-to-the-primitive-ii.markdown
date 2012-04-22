---
author: 7sharp9
layout: post
title: "Back to the Primitive II"
slug: Back-To-The-Primitive-II
date: 2012-04-22 10:23
comments: true
categories: [Programming Tales]
tags: [F#, C#, TPL, Async, Threading]
---
In the last post I discussed an asynchronous version of the `ManualResetEvent`, as promised this time we will be looking at an
 asynchronous version of the `AutoResetEvent`.  I'm using [Stephen Toubs post](http://blogs.msdn.com/b/pfxteam/archive/2012/02/11/10266923.aspx) 
as reference and we will be building a version that is functional in style that maps straight into asynchronous work flows without and conversion 
or adaptors.  

###What is an AutoResetEvent?
An `AutoResetEvent` can be described as a turnstile mechanism, it lets a single waiting person through before re-latching 
waiting for the next signal.  This is opposed to a `ManualResetEvent` which functions like an ordinary gate. Calling Set opens 
the gate, allowing any number of threads that are waiting to be let through. Calling Reset closes the gate.  

###AsyncAutoResetEvent
First of all here is the shape of the type that we will be building:

{% codeblock lang:fsharp %}
type AsyncAutoResetEvent =
    new : ?reusethread:bool -> AsyncAutoResetEvent
    member Set : unit -> unit
    member WaitAsync : unit -> Async<bool>
{% endcodeblock %}

Fairly simple: implied constructor, `Set` and `WaitAsync` members.  
### Implied Constructor
Thinking about this logically we may need the following items:

*   A queue mechanism to store asynchronous waiters - `let mutable awaits = Queue<_>()`.
*   A way of knowing if a signal has been made in the absence of any waiters - `let mutable signalled = false`.
*   We can also declare a short-circuit asynchronous workflow for the situation that `Set()` is called before `WaitAsync()` 
- `let completed = async.Return true`.  This will save us constructing an `AsyncResultCell<_>` and going though the 
rest of the asynchronous mechanism.  

Also notice that an optional parameter called `reusethread` is defined, we use the `?` prefix when defining it to make it 
optional.  We then make use of the `defaultArg` function to give it a default value of false if a one is not passed in.  This 
will be used in the `Set` operation to determine if the code will run on the same thread or a thread in the ThreadPool.  
{% codeblock lang:fsharp %}
open System
open System.Threading
open System.Collections.Generic
 
    type AsyncAutoResetEvent(?reusethread) =
		let mutable awaits = Queue<_>()
		let mutable signalled = false
        let completed = async.Return true
        let reuseThread = defaultArg reusethread false
{% endcodeblock %}
	
###WaitAsync()

The first step is to use  a locking construct to control access to the mutable queue `awaits`.  Inside this lock we 
check to see if `signalled` is true and if so we reset it to false and return our pre-built `completed` asynchronous workflow.  If 
signalled is false then we create a new `AsyncResultCell<_>` and add it to the queue then return the `AsyncResult` to the caller.  

{% codeblock lang:fsharp %}
        member x.WaitAsync() =
            lock awaits (fun () ->
                if signalled then
                    signalled <- false
                    completed
                else
                    let are = AsyncResultCell<_>()
                    awaits.Enqueue are
                    are.AsyncResult)
{% endcodeblock %}

###Set()

For the `Set` we use the lock function to control access to the mutable queue `awaits`.  Once inside the lock we use pattern matching 
to capture `awaits.Count` and `signalled`.  If there are waiters (`awaits.Count > 0`) then we dequeue an `AsyncResultCell<_>` from the
 queue and call `RegisterResult` passing in `AsyncOK(true)` to indicate a completion.  Notice that we also pass in the `reuseThread` 
boolean that was declared as part of the constructor.  If `reuseThread` is true then the notification to the waiter happens 
**synchronously**.  Please be aware that this causes the waiting asynchronous operation to happen **inside** the lock construct, use this 
with care!  Personally I would stick with the default of false to ensure that the operation is completed via the thread pool, unless you
have a performance critical reason and the waiting code that executes inside the lock is **very** fast.  

The second pattern match checks whether `signalled` is set to false, we then change its value to true.  This causes next `WaitAsync()`
 caller to get the short-circuited return value `completed` so that they do not have to have a `AsyncResultCell<_>` created and go though
 the whole async mechanism.  

The final pattern match is when there are no waiting callers and `signalled` has already being set, there is simply nothing to do in this situation.  

{% codeblock lang:fsharp %}
        member x.Set() =
            lock awaits (fun () ->
                match (awaits.Count, signalled) with
                | (x,_) when x > 0 -> 
                    let waiter = awaits.Dequeue()
                    waiter.RegisterResult(AsyncOk(true), reuseThread)
                | (_,y) when not y -> signalled <- true;()
                | (_,_) -> ())
{% endcodeblock %}

So there we have it, I could go on in this series and convert the other primitives that Stephen Toub describes but there should be 
enough information in these two posts to set you on your way.  If anyone would like me to complete the series then let me know.  I 
may well finish them off and post them on GitHub in the future, time permitting.

* * *
####Musical inspiration during the creation of this post:  
*   Pantera - Cowboys From Hell  
*   Cacophony - Go Off  
    {% img http://upload.wikimedia.org/wikipedia/en/a/a8/CowboysFromHell.jpg 125  Pantera - Cowboys From Hell %}
{% img http://upload.wikimedia.org/wikipedia/en/0/09/Cacophony_-_1988_-_Go_Off%21.jpg 125 Cacophony - Go Off %}

Thanks for tuning in, until next time...