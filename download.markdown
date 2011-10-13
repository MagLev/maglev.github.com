---
layout: default
title: Download MagLev
---

## Minimum Requirements
* 64-bit hardware. There are no plans for a 32-bit version.
* 64-bit Linux, Mac OS X, or Solaris X86 operating system
* a version of ruby and rake to use during installation
* root access so you can increase default shared memory settings (only during initial install)

There are three ways to install MagLev:
* Use the installer
* Install with Ruby Version Manager (RVM)
* Install from GitHub

## Use the installer

We recommend this version unless you want to participate in MagLev
development or want access to the absolute latest code. This version
changes more slowly than the github version, and it has undergone more
testing.

1. Select a directory to put MagLev in. Its name doesn't matter.
1. Download and unzip
   [MagLev-installer.zip](http://maglev.gemstone.com/files/MagLev-installer.zip)
   into the directory you've selected.
1. If this is the first time you're installing MagLev, run:

       ./installMaglev.sh 27001

   If you've already run `installMaglev.sh` on the machine, then you
   can use the upgrade script:

       ./upgradeMaglev.sh 27001

   `installMaglev.sh` runs one-time machine checks and configuration
   that ensure your hardware will run MagLev.  These mostly relate to
   shared memory settings. It's safe to run multiple times and it won't
   overwrite things it's already done.  `upgradeMaglev.sh` does not
   perform these checks.  Both scripts accept a version number as a
   parameter.

The installer is somewhat verbose. That's so you can more easily debug
installation problems.

Note: This installer/updater creates a separate directory for each new
version of MagLev. Reverting to an older version is as simple as stopping
MagLev, changing `$MAGLEV_HOME` to point to the prior version, and
restarting.

## Install with Ruby Version Manager (RVM)

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

## Install from GitHub

This version is best for MagLev contributors and for developers who want to
live on the bleeding edge or use git functionality. It can be less stable
than the zipped version.

1. Clone the MagLev github repository

       git clone git://github.com/MagLev/maglev.git

1. Install the VM.  If this is the first time you're installing MagLev,
   run:

       ./installMaglev.sh

   If you've already run `installMaglev.sh` on the machine, then you
   can use the upgrade script:

       ./upgradeMaglev.sh

To use MagLev, you need to run `install.sh` at least once.  After
that, everytime you `git pull`, you need to run `update.sh`
to ensure you have the corresponding GemStone 3.x binaries. Otherwise, you
might get version skew that prevents MagLev from running correctly.  I'll
tweet about any 'git only' updates, but if you're more than one update
behind you still might need to run `update.sh`.  You don't need to
know a version number to install. The installer/updater figures that out
from the version.txt file.

You'll have all the functionality of git, but unless you create a new
clone for each release, you may be unable to revert to an earlier version.
Why? The install/upgrade script creates a new copy of the MagLev data
repository including persistent kernel code. The scripts make one level of
backup, but we won't be providing migration scripts during Alpha.


## Download

You can download this project in either
[zip](http://github.com/MagLev/maglev/zipball/master) or
[tar](http://github.com/MagLev/maglev/tarball/master) formats.
