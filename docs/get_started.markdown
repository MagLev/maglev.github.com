---
title: Get Started with MagLev
layout: docs
---

# Get Started with MagLev

1. Install MagLev with [RVM](http://rvm.beginrescueend.com):

       rvm install maglev
       rvm use maglev

   Or, follow these more [detailed instructions](/docs/build.html) to build
   your own MagLev from source.

1. Check status of MagLev server

       $ maglev status
       MAGLEV_HOME = /users/pmclain/.rvm/rubies/maglev-1.0.0
       Status   Version    Owner    Pid   Port   Started     Type  Name
       ------- --------- --------- ----- ----- ------------ ------ ----
         OK    3.1.0     pmclain   48498 55390 Oct 31 10:06 Stone  maglev
         OK    3.1.0     pmclain   48499 55382 Oct 31 10:06 cache  maglev~3330cca3ca1d1f74

   If the server is not running, you'll see output like:

       gslist[Info]: No GemStone servers.

1. Run some Ruby!

       $ maglev-ruby $MAGLEV_HOME/examples/hello_maglev.rb
       Hello from:
         RUBY_VERSION: 1.8.7
         RUBY_ENGINE: maglev

# Next

* [Learn about MagLev](/docs/learn.html)
* Play with the [MagLev examples](https://github.com/MagLev/maglev/tree/master/examples)

