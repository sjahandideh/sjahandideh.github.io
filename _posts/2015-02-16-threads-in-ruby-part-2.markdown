---
layout: post
title: "Threads in Ruby (part two)"
date: 2015-02-16 05:35 UTC
tags: Threads, Ruby
---

![threads](/assets/threads/ruby-threads.jpg)

This is part two of [Threads](/blog/2014/11/25/threads/)

##How to make sure your code is threadsafe##


  When using a gem that needs your application to be threadsafe you need
  to make sure that it doesn't break the behaviour of your app.
  Especially if you don't have a full end-to-end test for your
  application this is the checklist of the things you need to double check:

  * **Class variables**
  * **Instance variables at the class level**
  * **Global variables starting with $**
  * **Constants**
  * **database pool size** must be greater than the maximum number of threads
    that can run at the same time in your app
  * **Mutables** such as strings should use .freeze to make them immutable
    and therefore threadsafe
    ```ruby
    FOO = "foo".freeze
    ```

    Also after the application has loaded, stop inserting methods into classes dynamically
    and try using threadsafe gems or at least be aware of the consequences if not.

  Now imagin you go through your codebase and find some class variables
  that you can't easily get rid of. There are a few ways that you can
  still make your code threadsafe. Although you should keep in mind that
  these solutions may affect the performance.

##Thread.current##


You can put your shared variables inside Thread.current.
The value that is set inside Thread.current can be shared safely inside
a running thread and not between threads. this can be a replacement for
class variables.
The following examples show a threadsafe and a thread-unsafe versions of
the same code and the different results they produce in Ruby MRI:

```ruby
class NamingThreadUnsafe

  @@name = 'shamim'

  def swapName
    if @@name == 'liloo'
        @@name = 'shamim'
    else
        @@name = 'liloo'
    end
  end

  def swapAndPrintName
    swapName
    puts @@name
  end

end

threads = []
5.times do

  threads << Thread.new do
    NamingThreadUnsafe.new.swapAndPrintName
  end

end

threads.each { |t| t.join }
```
This code will randomly generate different combinations of 'shamim' and 'liloo' such as:

liloo shamim shamim liloo liloo

liloo shamim liloo shamim liloo

and etc.

Whereas the following code generates the same result every time:

liloo liloo liloo liloo liloo

```ruby
class NamingThreadSafe

  def getName
    Thread.current['name'] || setName('shamim')
  end

  def setName(name)
    Thread.current['name'] = name
  end

  def swapName
    if getName == 'liloo'
        setName 'shamim'
    else
        setName 'liloo'
    end
  end

  def swapAndPrintName
    swapName
    puts getName
  end

end

threads = []
5.times do

  threads << Thread.new do
    NamingThreadSafe.new.swapAndPrintName
  end

end

threads.each { |t| t.join }
```
And the reason is that the second class "NamingThreadSafe" does not
share name between threads.

You can use Thread.current.object_id to namespace it. However by using
object_id you should be aware that if a thread dies and gets gorbage collected their key/values in the hash are never cleaned up.

##Mutex.synchronize##


Sometimes it is very difficult to make a legacy code threadsafe. In
such cases you may consider wrapping shared resources inside a Mutex:

```ruby
semaphore = Mutex.new

a = Thread.new {
  semaphore.synchronize {
    # access shared resource
  }
}
```

Anything that is wrapped inside the mutex is threadsafe. The
disadvantage of this approach is its effect on the performance of your application.
Therefore it should be used as the last resort.

##Future of threading in Ruby##


The whole point of GIL is that two threads can look at the same memory but if one thread modifies the memory, the other thread needs to start its operation over.

[This presentation](http://rubykaigi.org/2014/presentation/S-ReiOdaira) talks about Eliminating Giant VM Lock in Ruby in the future through Hardware Transactional Memory (HTM).

Transactional Memory attempts to simplify concurrent programming by allowing a group of load and store instructions to execute in an atomic way which means that it successfully changes the state of the app or has no effect at all.
HTM is a new hardware feature implemented in recent CPUs and allows multiple threads to access shared data without using locks.

In Ruby the researchers turned each one of Ruby operations (i.e loading a variable and method invocation) into a small transaction. If two threads execute operations that touch the same memory, the hardware will abort one of them so it can start the operation over therefore being Atomic. 
According to this research the performance increase was not huge but there were some hotspots that needed fixing: i.e Garbage Collection.
HTM is now available on Intel’s TSX extensions not yet on x86 CPUs.

