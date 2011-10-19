---
layout: docs
title: Persistent Code
---

## Persistent Code

MagLev persists Ruby code as well as Ruby objects.  You can install code
once, and share with all VMs.  MagLev lets you have either persistent or
non-persistent code.  By default (i.e., if you do nothing), when MagLev
reads a ruby file, the code is loaded transiently.  This is just like
regular ruby: each VM loads code, and when the VM exits, the code
disappears.

## <code>Maglev.persistent do ... end</code>

To follow along with this example, go to the hat trick directory:

    $ cd $MAGLEV_HOME/examples/persistence/hat_trick

`rabbit.rb` defines a simple class with one method:


{% highlight ruby %}
# This class defines the Rabbit, which just prints an ASCII art
# representation of a rabbit.
class Rabbit
  def inspect
    "\n () ()\n( '.' )\n(\")_(\")\n"
  end
end
{% endhighlight %}

If you run this file, not much happens.  A VM starts up, reads in the code,
defines a transient class named `Rabbit`, then exits, destroying all of
that work.  You cannot access `Rabbit` from a new VM:

    $ maglev-ruby rabbit.rb 
    [@miami hat_trick (master)]$ maglev-ruby -e 'puts Rabbit.new.inspect'
    ERROR 2730 , uninitialized constant Rabbit (NameError)

`maglev-ruby` provides a `-Mcommit` option that will run within the context
of a `Maglev.persistent` block, and do a commit on exit.  So we can do
this:

    $ maglev-ruby -Mcommit rabbit.rb 
    [@miami hat_trick (master)]$ maglev-ruby -e 'puts Rabbit.new.inspect'

     () ()
    ( '.' )
    (")_(")

The `maglev-ruby -Mcommit rabbit.rb` line is equivalent to:

{% highlight ruby %}
Maglev.persistent do
  load 'rabbit.rb'
end
Maglev.commit_transaction
{% endhighlight %}

We can also persistently monkey patch a class. Create a new file,
`breed.rb`:

{% highlight ruby %}
Maglev.persistent do
  # Monkey patch the Rabbit class
  class Rabbit
    def self.breed
      warren = []
      5.times { warren << Rabbit.new }
      warren
    end
  end
end
Maglev.commit_transaction
{% endhighlight %}

Since this version has the `persistent` block baked in, we just run the file:

    $ maglev-ruby breed.rb

And now we start a fresh VM, and we will immediately have access to the new
method:

    $ maglev-irb
    irb(main):001:0> Rabbit.breed
    => [
     () ()
    ( '.' )
    (")_(")
    ,
     () ()
    ( '.' )
    (")_(")
    ,
     () ()
    ( '.' )
    (")_(")
    ,
     () ()
    ( '.' )
    (")_(")
    ,
     () ()
    ( '.' )
    (")_(")
    ,
    ]
    irb(main):002:0>

# Details

For details, read the [Persistence Documentation](https://github.com/MagLev/maglev/blob/master/docs/persistence-api.rdoc).

