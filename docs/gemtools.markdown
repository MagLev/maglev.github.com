---
title: GemTools on MagLev
layout: docs
gemtools_version: GemTools-1.0-beta.8.7-310x
---

# GemTools Smalltalk Browser for MagLev

GemTools is a Smalltalk Development environment that can be used to view
the Smalltalk source code built into the MagLev image.  You can view the
same information with Topaz (the built-in command line debugger/inspector),
but GemTools provides a nice GUI for interacting with the image.

The GemTools download includes everything you need to run Pharo Smalltalk
on Linux and OSX.

## Install GemTools

1. Download [{{page.gemtools_version}}](http://seaside.gemstone.com/squeak/{{page.gemtools_version}}.zip)
1. Unzip the archive.
   You should have a directory named `{{page.gemtools_version}}.app`.

   OSX users may want to move it to `/Applications`.

## Run GemTools

1. Ensure the MagLev stone is running:

       $ cd $MAGLEV_HOME
       $ rake maglev:start

1. Ensure a `netldi` process is running:

       $ cd $MAGLEV_HOME
       $ rake netldi:start

1. Start GemTools

   OSX users can either `open {{page.gemtools_version}}.app` or open it from
   the Finder as a normal OSX app.

   Linux users can run `{{page.gemtools_version}}.app/Pharo.sh`.

1. Connect to MagLev

   After the Pharo Smalltalk process opens, you'll need to connect to the
   MagLev stone.  To do that, select "MagLev" in the GemTools Launcher
   window, and then click "Login".

   The first time you login, you'll be asked for your first and last name.
   This allows the Monticello source code system to know who is making
   changes (just like adding your user info into `~/.gitconfig`).

## Browse some source code

After you've logged into the image, you can look at some MagLev code by:

1. Click the "Find..." button
1. Select "Hierarchy browser"
2. Enter "Kernel" into the dialog and hit enter (or click "Ok").

You can explore the source code from here.

## Sharing `netldi` between Smalltalk and MagLev

If you are a GemStone/S Smalltalk user, and would like Smalltalk and MagLev
to use the same locks directory (one `netldi` to rule them all), then run
`$MAGLEV_HOME/setupLocks.sh`.
