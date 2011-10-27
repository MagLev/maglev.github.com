---
layout: docs
title: Learn MagLev
---

# Learn about MagLev

## Basic MagLev Object Persistence

MagLev brings a transparent, distributed object space to Ruby.
To try it yourself, make sure you've [installed
MagLev](/docs/get_started.html), and that the MagLev server is running.

First, fire up a `maglev-irb` session (since we'll be using two different
irb sessions, I've set the prompts to "One" and "Two" to make it easier to
follow along).

    $ maglev-irb
    One :001:0>

MagLev comes pre-seeded with a persistent hash named
`Maglev::PERSISTENT_ROOT`.  It is a convenient starting place to put
objects that you want to share with other VMs.  We'll put a message into
that hash and then save it:

    One :001:0> Maglev::PERSISTENT_ROOT[:demo] = "Message from maglev-irb #1"
    => "Message from maglev-irb #1"
    One :002:0> Maglev.commit_transaction
    => true

Now, we can start a new `maglev-irb` session and see the message:

    $ maglev-irb
    Two :001:0> Maglev::PERSISTENT_ROOT[:demo]
    => "Message from maglev-irb #1"

Cool!

MagLev's persistence model is persistence by reachability.  That means that
when MagLev persists (saves) `an_object`, all objects reachable from
`an_object` (by following references held in instance variables) are also
persisted. `Maglev::PERSISTENT_ROOT` is simply a persistent hash, so when
it is saved, all objects it references (i.e., the keys and values) are also
saved (as well as any objects they reference, and so on).

So, we can create a "complex" object, and send it from irb process Two back
to irb process One.  First, we create a normal array and fill it with
objects:

    Two :002:0> my_array = Array.new
    => []
    Two :003:0> my_array << "Hello from #2"
    => ["Hello from #2"]
    Two :004:0> my_array << Time.now
    => ["Hello from #2", Mon Oct 17 13:45:52 -0700 2011]

Then, we hook it into the persistent graph by putting the array into
`PERSISTENT_ROOT`, and committing the change:

    Two :005:0> Maglev::PERSISTENT_ROOT[:demo] = my_array
    => ["Hello from #2", Mon Oct 17 13:45:52 -0700 2011]
    Two :006:0> Maglev.commit_transaction
    => true

Since PERSISTENT_ROOT is persistent, and it references the array, and the
array references both the string and the time object, all of that will now
be available to other VMs.  We can check by switching to the first
`maglev-irb` session and taking a look:

    One :003:0> Maglev::PERSISTENT_ROOT[:demo]
    => "Message from maglev-irb #1"

Hmmm...the first irb session still sees the old value.  To coordinate
changes to the shared object space, MagLev uses transactions.  We need to
refresh our transactional view in order to see the new data:

    One :004:0> Maglev.abort_transaction
    => Maglev::System
    One :005:0> Maglev::PERSISTENT_ROOT[:demo]
    => ["Hello from #2", Mon Oct 17 13:45:52 -0700 2011]

Now we see the new data from irb Two.   Most of the standard Ruby objects
are already capable of being persisted (there are a few that don't make
sense to see in another VM, like Mutex and Socket).  You can even
persist `Proc`s and `Thread`s and run them on other VMs:

    One :006:0> ::PERSISTENT_ROOT[:run_me] = Proc.new { puts "Hi from another VM" }
    => #<Proc>
    One :007:0> Maglev.commit_transaction
    => true

And we can run it from the other VM:

    Two :007:0> Maglev.abort_transaction
    => Maglev::System
    Two :008:0> Maglev::PERSISTENT_ROOT[:run_me].call
    Hi from another VM

You could even make a simple [worker
queue](https://github.com/jc00ke/maglev-q) in a few lines of Ruby by
passing procs between VMs.

Threads take a little more effort, because there are a few pitfalls,
so bear with me:

    One :008:0> Thread.primitive 'make_persistable', 'convertToPersistableState'
    => Thread

First, we installed an additional primitive that allows persisting Threads.

    One :009:0> Thread.start { callcc {|cc| $cont = cc}; p "Run after One pretty callcc" }
    "Run after One pretty callcc"
    => #<GsProcess:0xdf69e01 false>
    One :010:0> $cont
    => #<Continuation:0xecfc401 @_st_process=#<GsProcess:0xdf6a901 sleep>>
    One :011:0> $cont.instance_variable_get(@_st_process).make_persistable
    => #<GsProcess:0xdf6a901 sleep>
    One :012:0> Maglev::PERSISTENT_ROOT["cc"] = $cont
    => #<Continuation:0xecfc401 @_st_process=#<GsProcess:0xdf6a901 sleep>>
    One :013:0> Maglev.commit_transaction
    => true

Phew, that was a lot of work. What did we do here? Well, we started a thread and created a
continuation. We keep it around in a global variable. We can see in the inspection output
that the continuation has a reference to a _copy_ of the Thread that created it. That's what
we want to persist. Because Threads carry a lot of VM state, and keeping that persistable
could potentially slow down execution, we have to explicitely mark the continuation's Thread
persistable.

On to the second VM:

    Two :009:0> Maglev.abort_transaction
    => true
    Two :010:0> $cont = Maglev::PERSISTENT_ROOT["cc"]
    => #<Continuation:0xecfc401 @_st_process=#<GsProcess:0xdf6a901 sleep>>
    Two :011:0> Thread.start { $cont.call }
    "Run after One pretty callcc"
    => #<GsProcess:0xdd33b01 sleep>

Wow. We just moved a complete stack on to a different VM and resumed it there.

So much for examples, more example code can be found in the
[hat trick example](https://github.com/MagLev/maglev/tree/master/examples/persistence/hat_trick)
that also demonstrates basic MagLev persistence.

