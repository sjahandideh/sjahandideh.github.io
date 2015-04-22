---
layout: post
title: "Threads in Ruby (part one)"
date:  "2014-11-25 00:00 UTC"
tags: Threads, Ruby
---

![threads](/assets/threads/ruby-threads.jpg)

##Purpose of this post##
I recently gave a talk about [Threads in Ruby](https://github.com/sjahandideh/threads_slides) but since a few people believed this was a hardcore topic for a 15-minute talk, I thought I would elaborate in this post.

##Why do I need to know about threads?##
My main purpose to discuss threads is not for the audience to start writing multithreaded code but to have a good idea of what is happening to their application when they are using multithreaded gems in their codebase.

Nowadays a good number of us are using [Sidekiq](https://github.com/mperham/sidekiq) for background processing or [Puma](https://github.com/puma/puma) as an efficient web server and I'm sure there will be more of such gems soon enough so I believe this makes it essential for us to know how our applications behave when we use such gems and how we can get the most out of them.

##Threads vs Process Forks##

[Fork](http://en.wikipedia.org/wiki/Fork_(system_call)) means that a process creates a copy of itself.
[Threads](http://en.wikipedia.org/wiki/Thread_(computing)) have one parent process and share memory space.

A good example is the difference between [Resque](https://github.com/resque/resque) and [Sidekiq](https://github.com/mperham/sidekiq).
Resque uses forks whereas Sidekiq uses threads instead.

To fork a process, the operating system will create a separate address space and copy all the memory segments of the parent process. This also makes it more expensive for forked processes to switch context since OS needs to save and reload everything. Also, if the parent dies while there are children that haven’t exited, then those children become zombies.

![zombie processes](/assets/threads/zombies.png)

Threads on the other hand, have considerably less overhead since they share address space, memory, and allow easier communication.
Also, with threading you do not have to worry about zombie processes since all threads die when the process dies, avoiding the issue of zombies.

##Concurrency vs. Parallelism##

Rob Pike explains the difference between these two concepts very nicely in [this video](http://vimeo.com/49718712).

In a nutshell:
* Concurrency means *dealing* with a lot of things at once
* Parallelism means *doing* a lot of things at once

This is a very important difference. I give you an example:
Imagine, you have 2 threads that are doing the exact sequence of operations (A, B and C). Operation B involves some IO.

Now thread 1 uses CPU to execute A while thread 2 is waiting.
Thread 1 then switches to B and does some IO stuff. Meanwhile thread 2 uses CPU to do A.
This process includes Concurrency meaning threads are doing different things concurrently and minimizing the time that CPU is idle.<br>
CPU is still used by only one thread at each point of time.

Parallelism on the other hand means that thread 1 and thread 2 are using CPU at once.<br>
Multiple threads use CPU at the same time.

Threads make Concurrency as well as Parallelism possible.

##Performance##
Concurrency alone can increase performance even without Parallelism. If the workload of a thread blocks on I/O, Ruby can context-switch to other threads and do other work until the I/O finishes. This can happen when the workload reaches out to an external API or is accessing the file system.

##Memory##
Threads are more memory efficient compared to processes. Main reasons are the following:
* They share the same address space.
* They do not need a separate Virtual memory allocation
* Context switching that is the process of storing and restoring the state (context) of a process or thread so that execution can be resumed from the same point at a later time is cheaper with threads.

##Threadsafety##
Threadsafety tries to solve the problem of multiple threads that share variables try to modify them at the same time.
There are [a few solutions](http://en.wikipedia.org/wiki/Thread_safety#Implementation_approaches) for this problem one of which is Lock/Semaphore/Mutex.

Mutex(Mutual exclusion) is used to synchronize access to a section of a code so only one thread at a time can access it.
This is the way Ruby handles threadsafety. We’ll look into that a bit later.

###Threadsafety in Ruby###
Ruby is not threadsafe. A good example is data structures such as Array and Hash. However different Implementations of ruby behave differently:

The difference between Thread in MRI vs. JRuby and Rubinius:

* **MRI**: It has GVL(Giant VM Lock) or GIL (Global Interpreter Lock) which is wraps all your code in a Mutex and therefore prevents Parallelism except for one case:
  If you have one thread that's waiting on IO (i.e. waiting for a response from the DB), MRI will allow another thread to run in parallel.
In MRI even when writing multi-threaded Ruby only a single thread is on-CPU at a point in time.

* **JRuby** and **Rubinius**:
  Rather than a global lock, key parts of the runtimes are protected with fine-grained locks so that their internals are thread-safe which allows your code to be truly parallel.

This means that if you run a multithreaded code such as this:

```ruby
array   = []
threads = []

20.times do
  threads << Thread.new do
    15.times do
      array << 'blah'
    end
  end
end

threads.map(&:join)

puts array.size
```

The result in MRI is consistent but in JRuby and Rubinius it changes every time.

Rails however is different. The vast majority of normal, day-to-day Rails programming is inherently threadsafe.
There's an old but informative [article](http://yehudakatz.com/2010/08/14/threads-in-ruby-enough-already/) on this topic that I encourage you to read.
Since Rails 4 threadsafety is [enabled by default](http://tenderlovemaking.com/2012/06/18/removing-config-threadsafe.html).

###Next post###
This is the end of part one. In the next post I'll dig into the things you need to watch for in order to have a threadsafe code. I'll also talk about the future of threading in Ruby and where it is heading.
See you soon!

<img style="float: right" src="/assets/threads/ruby-thread.jpg">

The slides for my talk exists [here](https://github.com/sjahandideh/threads_slides)

The video of the talk exists [here](https://vimeo.com/113046082) kindly added by [codr.tv](http://codr.tv/)

