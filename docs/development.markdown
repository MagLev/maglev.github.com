---
title: MagLev Development
layout: docs
---
# Developing MagLev itself

## Step 1: Install MagLev from GitHub

First, build MagLev from source [as described
here](/docs/build.html). Make sure to run the tests and check that
they pass.

## Step 2: Setup your environment

To work with MagLev from the source tree, you have to run commands
from the <tt>bin</tt> directory of the repository. Some of the scripts
and rake tasks written for developers additionally require you to set
a few environment variables. So, assuming you are in the top-folder of
your MagLev repository, run the following:

    $ export MAGLEV_HOME="$(pwd)"
    $ export GEMSTONE_GLOBAL_DIR=$MAGLEV_HOME
    $ export PATH="$PATH:$MAGLEV_HOME/bin"
    $ export GEMSTONE="$MAGLEV_HOME/gemstone"
    $ source "$MAGLEV_HOME/gemstone/bin/gemsetup.sh"

## Step 3: Building a stone from first principles

If you've successfully followed the [build
instructions](/docs/build.html), you already have a working image. But
as a developer, you might run into situations where you break your
stone and/or build process in one of several ways, so we'll walk
through the process of manually creating a fresh image from the
Smalltalk image provided with Gemstone/S.

At each step I'll explain what's going on and how you might want to
develop in that particular area.

<div style="padding: 2em;">
<em>Note:</em>
Most of these commands have to be run with another
Ruby. MagLev by default doesn't include a script named <tt>ruby</tt>,
only <tt>maglev-ruby</tt>. To avoid confusion, make sure
<tt>$MAGLEV_HOME/bin</tt> is at the end of your
<tt>$PATH</tt>. Whenever we have to run something with MagLev, I will
always use <tt>maglev-ruby -S</tt> style invocations.
</div>

### Clean everything

If at any time your image seems broken or you are afraid that some
code has been persisted that you don't know how else to remove, you
can just delete the Ruby image using the clobber task.

    $ rake build:clobber

### Prepare the Smalltalk image

Ruby and Smalltalk run on the same VM and image, but there are a few
changes that the base Smalltalk image needs in order to run even basic
Ruby code. That's the code you will find in the <tt>src/smalltalk/ruby</tt>
directory. These are extensions to the core classes of the Smalltalk
system, and you shouldn't normally have to worry about those. If you
ever do need to change any of them, you will have to clobber and start
afresh. You can create a basic Ruby image using

    $ rake build:filein

This will <tt>file in</tt> (Smalltalker speak for loading code and
persisting it in an image) the basic Ruby code, as well as Monticello
(a Smalltalk VCS) and the basic stuff you need to connect to the Stone
from a Smalltalk image using [GemTools](/docs/gemtools.html).

If you run this task twice in succession, the second run will fail and
possibly leave your image in an unusable state.

### Load Ruby core

You know how, on MRI, a lot of core functionality is implemented in C?
If you don't like C, then on MagLev, the situation is a little nicer
for you. While the VM, the primitives and the parser are implemented
in C/C++, lots of what makes up the Ruby core library is actually
written in Smalltalk. That code used to be managed in Monticello, and
is now included in file-based form in <tt>src/smalltalk/mcz</tt>.

    $ rake build:mczdir

That invocation will load the Ruby core code (as far as it is written
in Smalltalk) into the stone. If you want to develop on MagLev, you
might work a lot with this code, and there's two ways to do that:

##### For Smalltalkers

Developers who know either Gemstone/S or Squeak/Pharo and are
comfortable working with Monticello and the image based tools should
get the [GemTools for MagLev](/docs/gemtools.html).

Monticello has been modified in MagLev to update the filetree whenever
a new version of the Monticello package for MagLev is saved, so you
don't need to worry about keeping the code in sync. If you develop
from the GemTools browser, you won't have to repeat the
<tt>build:mczdir</tt> command either, because all your changes are
immediately persisted in your image and applied to your running code.

##### For friends of the text editor

You can just edit files in the <tt>src/smalltalk/ruby/mcz</tt>
directory. If you add a class, make sure to include its definition in
the file <tt>class_definitions.gs</tt>, and create a separate file
(named after the class) to hold its methods. Always make sure that
somewhere above the first method definition is at least one line that
sets the class and one that sets the category. Those directives apply
to all following method definitions (preferably make those two lines
the first in the file). Also, your category <strong>must</strong>
start with <tt>*maglev</tt> <strong>or</strong> your class has to be
in the <tt>MagLev</tt> category, to make sure Smalltalkers can still
work with the image based tools.

### Create a stone

These last two steps have left us with an image called
<tt>extent0.ruby.dbf</tt>. It's in the <tt>fileintmp</tt>
directory. Now, to create a stone, MagLev expects the image file to be
in <tt>bin/</tt>, but we'll want to be able to just run the
<tt>build:mczdir</tt> task again to update Smalltalk code in our
stones. So let's link the image file instead:

    $ ln -s fileintmp/extent0.ruby.dbf bin/extent0.ruby.dbf

Then create a stone

    $ rake stone:create[maglev]

This will copy the image file to
<tt>data/maglev/extent/extent0.ruby.dbf</tt> and that's what we'll be
running off when running Ruby code. So we symlink that, too:

    $ rm data/maglev/extent/extent0.ruby.dbf
    $ ln -s fileintmp/extent0.ruby.dbf data/maglev/extent/extent0.ruby.dbf

### Load the Ruby Ruby core

Now, finally, we can actually run some Ruby code. Nothing too serious, yet,
but enough to load the other half of Ruby core written in Ruby. Much
of that code was actually shared with the [Rubinius](http://rubini.us)
project at some point in the past.

    $ rake maglev:start
    $ rake maglev:reload_prims

This starts the stone process and loads the code below
<tt>src/kernel</tt>. That means all the Ruby code in there will be
parsed, compiled and committed to the image, so subsequent VMs don't
have to reload all of the Ruby core when they start up. Whenever you
change any code in there, you'll have to run the
<tt>maglev:reload_prims</tt> task again.

If everything went as it should, you can now use MagLev to run useful
Ruby code. Like this:

    $ maglev-ruby -e 'p MAGLEV_VERSION'
    "1.0.0"

## Step 4: Great rejoicing

That's it, you have a basic development environment. You can now pick a
feature or try and fix an issue. If you don't know where the problem
is, look around the code and get a feel for what goes where.

There's a few more things to talk about, like how to work with
branches, where to write tests and how to debug using Topaz and
GemTools. We'll update this page as soon as possible with details on
those topics, but this should be enough to get you started. There's
also a <tt>docs/</tt> directory in the repository with notes on
development related topics.

... more coming soon ...
