---
title: MagLev Build
layout: docs
---
# Build MagLev from Source

## Step 1: Install the MagLev source:

    $ git clone git://github.com/MagLev/maglev.git
    $ export MAGLEV_HOME=$PWD/maglev

## Step 2: Install VM

    $ cd $MAGLEV_HOME
    $ ./install.sh

You may specify a particular version of the VM, see the detailed
instructions at <https://github.com/maglev/maglev> for details.

## Step 3: Build MagLev image and start it

The following command will transform the base Smalltalk image that
comes with GemStone/S
(<tt>$MAGLEV_HOME/gemstone/bin/extent0.dbf</tt>) into a Ruby image and put
it in <tt>$MAGLEV_HOME/bin/extent0.ruby.dbf</tt>:

    $ rake build:maglev
    $ rake maglev:start

Now you should be able to run ruby on MagLev:

    $ maglev-ruby -e 'puts "Hello from #{RUBY_ENGINE}"'
    Hello from maglev

You can run the tests:

    $ rake tests

# How it all fits together

MagLev is a 64-bit open source (MIT licensed) implementation of
the Ruby programming language and libraries built on top of VMware's
GemStone/S 3.1 Virtual Machine.

The MagLev VM takes full advantage of GemStone/S JIT to native code
performance, distributed shared cache, fully ACID transactions, and
enterprise class NoSQL data management capabilities to provide a
robust and durable programming platform. It can transparently manage
a much larger amount (terabytes) of data and code than will fit in memory.
There are no restrictions on what types of objects, classes, blocks,
threads or continuations that can be stored and executed.

MagLev is layered on top of the GemStone/S Virtual Machine, which
is written in C++ for optimum performance.  To build MagLev, we
start with a basic GemStone/S system that has been extended especially
for MagLev. Once the MagLev VM is bootstrapped, and the requisite
primitives are loaded, it's ready to run ruby code much like other
implementations.  The difference is that only MagLev has an integrated
distributed object cache with transparent object persistence where
multiple concurrent VM's interact transactionally via shared memory.
To handle larger amounts of data, the MagLev system has multi-threaded
garbage collection that runs in a separate VM. Due to it's multi-language
nature, MagLev can utilize code written in Ruby, Smalltalk, or C++ (through
its FFI to C++, as well as ruby extensions writen directly in C++.)

The build steps described above add the base Ruby classes and objects
on top of a standard GemStone/S system, and persist them as a base
MagLev image (<tt>$MAGLEV_HOME/bin/extent0.ruby.dbf</tt>).

The scripts that load the ruby objects into the image are a combination of
rakefiles (<tt>rakelib/build.rake</tt>) and topaz scripts.  Topaz is a C++
program that runs a GemStone VM, and provides a command line interface to
that VM; see the
[Topaz Programming Guide](http://community.gemstone.com/download/attachments/6816350/GS64-Topaz-3.0.pdf?version=1).

