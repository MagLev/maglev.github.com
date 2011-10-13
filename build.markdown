---
title: MagLev Build
layout: docs
---
# Build MagLev from Source

## Step 1: Install the MagLev source:

    $ git clone git://github.com/MagLev/maglev.git
    $ export MAGLEV_HOME=$PWD/maglev

## Step 2: Install VM

See the detailed instructions at <https://github.com/maglev/maglev> for the
value of "NNN":

    $ cd $MAGLEV_HOME
    $ ./install.sh NNN

## Step 3: Build MagLev image and start it

The following command will turn the smalltalk image
(<tt>$MAGLEV_HOME/gemstone/bin/extent0.dbf</tt>) into a Ruby image and put
it in <tt>$MAGLEV_HOME/bin/extent0.ruby.dbf</tt>:

    $ rake build:image
    $ rake stone:create[maglev]
    $ rake maglev:start

Now you should be able to run ruby on MagLev:


    $ mruby -e 'puts "Hello from #{RUBY_ENGINE}"'
    Hello from maglev

You can run the tests:

    $ rake tests

# How it all fits together

MagLev is layered on top of the GemStone/S Smalltalk Virtual Machine.  To
build MagLev, we start with s pure Smalltalk system, and then add layers of
Ruby features on top of it.  To do this, we use the Smalltalk tools and
methods to introduce the core ruby extensions needed, and then run the pure
Ruby definitions after that.

## VM versus image

The GemStone/S VM is just a C++ program that runs and manages an object
memory.  The VM has an Object Manager that knows how to create, delete and
otherwise manage objects. The objects live in a large chunk of memory known
as "object memory".  When the VM starts up, it allocates space for object
memory, and then loads objects from the image (stored in the stone), puts
them into object memory and activates them.

The base Smalltalk objects and classes (Object, Class, Array, etc.)  are
pre-loaded in the Smalltalk image that comes with the VM
(<tt>$MAGLEV_HOME/gemstone/bin/extent0.dbf</tt>).

The build steps described above add the base Ruby classes and objects on
top of the Smalltalk image to create the base MagLev image
(<tt>$MAGLEV_HOME/bin/extent0.ruby.dbf</tt>).  <tt>extent0.ruby.dbf</tt> is
then copied as the initial extent when you create a new MagLev stone
(<tt>rake stone:create[maglev]</tt>).

The same GemStone/S VM will run objects served out of either the Smalltalk
or MagLev extents (they are just plain old objects, as far as the VM and
Object Manager are concerned).

Once you start persisting your own objects, they too will be stored in the
stone alongside the base Smalltalk and Ruby objects.

The scripts that load the ruby objects into the image are a combination of
rakefiles (<tt>rakelib/build.rake</tt>) and topaz scripts.  Topaz is a C
program that runs a GemStone VM, and provides a command line interface to
that VM; see the
[Topaz Programming Guide](http://community.gemstone.com/download/attachments/6816350/GS64-Topaz-3.0.pdf?version=1).

