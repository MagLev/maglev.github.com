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
from the <tt>bin</tt> directory of the repository. You can link the
checkout to <tt>rbenv/.versions/maglev-head</tt> if you use rbenv, or
manage your <tt>PATH</tt> in some other way. It's important you do
manage it somehow, because some commands have to be run with another
Ruby's <tt>rake</tt>, and some can be run from MagLev.

Some of the scripts and rake tasks written for developers additionally
require you to set a few environment variables. So, assuming you are
in the top-folder of your MagLev repository, run the following:

    $ export MAGLEV_HOME="$(pwd)"
    $ export GEMSTONE_GLOBAL_DIR=$MAGLEV_HOME
    $ export GEMSTONE="$MAGLEV_HOME/gemstone"

## Step 3: Building a stone from first principles

If you've successfully followed the
[build instructions](/docs/build.html), you already have a working
image. But as a developer, you might run into situations where you
break your stone and/or build process in one of several ways, so we'll
walk through the process of manually creating a fresh image from the
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
is now included in file-based form in <tt>src/packages</tt>. This is
actually a [FileTree](https://github.com/dalehenrich/FileTree)
repository, so you can still manage those packages from Monticello in
[GemTools](/docs/gemtools.html).

    $ rake build:packages

That invocation will load the Ruby core code (as far as it is written
in Smalltalk) into the stone. If you want to develop on MagLev, you
might work a lot with this code, and there's two ways to do that:

##### For Smalltalkers

Developers who know either Gemstone/S or Squeak/Pharo and are
comfortable working with Monticello and the image based tools should
get the [GemTools for MagLev](/docs/gemtools.html).

Monticello uses FileTree to update the files whenever a new version of
the Monticello package for MagLev is saved using the UI, so you don't
need to worry about keeping the code in sync. If you develop from the
GemTools browser, you won't have to repeat the <tt>build:packages</tt>
command either, because all your changes are immediately persisted in
your image and applied to your running code.

##### For friends of the text editor

You can just edit files in the <tt>src/packages</tt> directory. If you
add a class, check how other classes work (separate folder for the
class, json file with the definition and method names, folders for
instance methods and class methods). If you need to extend a core
Smalltalk class, take a look at the folders ending in
<tt>.extension</tt>.

### Create a stone

These last two steps have left us with an image called
<tt>extent0.ruby.dbf</tt>. It's in the <tt>fileintmp</tt>
directory. Now, to create a stone, MagLev expects the image file to be
in <tt>bin/</tt>, but we'll want to be able to just run the
<tt>build:packages</tt> task again to update Smalltalk code in our
stones. So let's link the image file instead:

    $ ln -s $MAGLEV_HOME/fileintmp/extent0.ruby.dbf bin/extent0.ruby.dbf

Then create a stone

    $ rake stone:create[maglev]

This will copy the image file to
<tt>data/maglev/extent/extent0.ruby.dbf</tt> and that's what we'll be
running off when running Ruby code. So we symlink that, too:

    $ rm data/maglev/extent/extent0.ruby.dbf
    $ ln -s $MAGLEV_HOME/fileintmp/extent0.ruby.dbf data/maglev/extent/extent0.ruby.dbf

### Load the Ruby core

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
feature or try and [fix an issue](https://github.com/MagLev/maglev/issues).
If you don't know where the problem is, look around the code and get a feel 
for what goes where.

## Further Topics

#### Looking at code with Topaz

If you look through the Ruby code in <tt>src/kernel</tt>, you will
find class-level calls to <strong><tt>primitive</tt></strong>. These
bind Ruby method names to Smalltalk methods. These Smalltalk methods
are often found under either the <tt>src/smalltalk</tt> or
<tt>src/packages</tt> directories, but they may also be just in the
image. To look at what's currently available in the image, you can use
Gemtools (see above), or <strong>topaz</strong>, which ships with
GemStone/S.

Provided your environment variables are set, and you are in your
maglev checkout, run this:

    $ bin/maglev startnetldi
    $ gemstone/bin/topaz
	*snip*
	topaz> set user DataCurator password swordfish gemstone maglev
	topaz> login
	*snip*
	successful login
	topaz 1>

This logged you into the stone "maglev" as DataCurator (the default user).

	topaz 1> look Module >> rubyName
    
    category: '*maglev-runtime'
    method: Module
    rubyName
      ^ name
    
    %
    topaz 1>

You've just looked at the method rubyName in the class module, as it
is currently loaded in the stone (useful for comparing what's in your
files and what's in the stone).

    topaz 1> set class Object
	topaz 1> rubylook meth inspect
    method: Object
      inspect#0*&
        <bridge method, objId 188217345>
    method: Object
      def inspect
        # sender of _inspect must be in env 1
        self.__inspect
      end
    # method starts at line 324 of /home/tim/Devel/maglev/src/kernel/bootstrap/Object.rb
	topaz 1>

And here you looked at the Ruby implementation of
Object#inspect. Notice how the stone sees a method
<tt>inspect#0*&amp;</tt> - this encodes the information how many
arguments a method takes.

    topaz 1> rubylook meth class
    method: Object
      class#0*&
        <bridge method, objId 188189697>
    method: Object
    class
    "return first non-virtual non-singleton class at or above receiver's class.
    
     The old method  Object>>_class is not implemented in extent0.ruby.dbf
    "
    <primitive: 730>
    ^ self _primitiveFailed: #class
    topaz 1>

This is how a primitive looks in the stone. You have a bridge method
like for a pure Ruby method, but as implementation, the stone has the
Smalltalk method class (which is VM primitive 730).

Topaz has many more features, you can run and edit code. Type
<tt>help</tt> and/or refer to the
[GemStone/S Programming Guide](http://community.gemstone.com/download/attachments/6816350/GS64-ProgGuide-3.0.pdf)
to learn more. This will also be useful when
[debugging](/docs/issue_tracking.html).

