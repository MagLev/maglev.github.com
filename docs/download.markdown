---
layout: default
title: Install MagLev
---
# Install MagLev

There are two ways to install MagLev:
* [Install with Ruby Version Manager (RVM)](#install_with_ruby_version_manager)
* [Install from GitHub](#install_from_github)

## Minimum Requirements

* 64-bit hardware. There are no plans for a 32-bit version.
* 64-bit Linux, Mac OS X, or Solaris X86 operating system
* a version of ruby and rake to use during installation
* root access so you can increase default shared memory settings (only during initial install)

# Install with Ruby Version Manager

[RVM](http://rvm.beginrescueend.com) is useful if you want to compare
behavior of various Ruby implementations or versions of MagLev.  Install
using:

    rvm install maglev

See the [rvm home page](http://rvm.beginrescueend.com) for details of using
RVM.

RVM will automatically start a MagLev server when you `rvm use
maglev`.  That server is left running if you `rvm use any_other_Ruby`.

To stop your MagLev server:

    rvm use maglev
    maglev stop

Note: `maglev stop` will only stop the version of MagLev currently in use.
If you're using more than one, you'll have to `maglev stop` each of them -- e.g.

    rvm use maglev-26436
    maglev stop
    rvm use maglev-head
    maglev stop

ProTip: To kill all the MagLev servers rather than shut them down gracefully, run
`ps -ef | grep /sys/stoned` and kill the `stoned` processes that show up.
It takes a short time before they shut down, as they do a bit of cleanup first.

Note: When running rake from MagLev, you cannot perform tasks which
affect MagLev itself, like `rake maglev:stop`. You have to use the
`bin/maglev` shell script instead.

# Install from GitHub

This version is best for MagLev contributors and for developers who want to
live on the bleeding edge or use git functionality.

1. Clone the MagLev github repository

       git clone git://github.com/MagLev/maglev.git

1. Install the VM.  If this is the first time you're installing MagLev,
   run:

       ./install.sh

   Or, if you've already run `install.sh` on the machine, then you can use
   the upgrade script:

       ./upgrade.sh

To use MagLev, you need to run `install.sh` at least once.  `install.sh`
does everything `upgrade.sh` does, but also does some initial configuration
of your system to ensure MagLev's shared caching and other features will
work.

Everytime you `git pull`, you should run `update.sh` to ensure you have the
corresponding GemStone 3.x binaries. Otherwise, you might get version skew
that prevents MagLev from running correctly.  I'll tweet about any 'git
only' updates, but if you're more than one update behind you still might
need to run `update.sh`.  You don't need to know a version number to
install. The installer/updater figures that out from the `version.txt`
file.

You'll have all the functionality of git, but unless you create a new
clone for each release, you may be unable to revert to an earlier version.
Why? The install/upgrade script creates a new copy of the MagLev data
repository including persistent kernel code. The scripts make one level of
backup, but we won't be providing migration scripts during Alpha.


# Download

You can download this project in either
[zip](http://github.com/MagLev/maglev/zipball/master) or
[tar](http://github.com/MagLev/maglev/tarball/master) formats.
