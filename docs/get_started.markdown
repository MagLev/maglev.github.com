---
title: Get Started with MagLev
layout: docs
---

# Install and Start MagLev

1. Install MagLev with [RVM](http://rvm.beginrescueend.com):

       rvm install maglev

   Or, follow the more [detailed instructions](/download.html).

1. Setup your environment:

       export MAGLEV_HOME=<path_to_the_directory_containing_this_README>
       export PATH=$MAGLEV_HOME/bin:$PATH

1. Start the MagLev server

       $ cd $MAGLEV_HOME
       $ rake maglev:start

1. Check status of MagLev server

       $ cd $MAGLEV_HOME
       $ rake
       Status   Version    Owner    Pid   Port   Started     Type  Name
       ------- --------- --------- ----- ----- ------------ ------ ----
         OK    3.0.1     pmclain   33046 58301 Jul 19 12:31 Stone  maglev
         OK    3.0.1     pmclain   33047 58291 Jul 19 12:31 cache  maglev~d646de6d79d7b341

   If the server is not running, you'll see output like:

       $ rake
       gslist[Info]: No GemStone servers.

1. Run some Ruby!

       $ maglev-ruby $MAGLEV_HOME/examples/hello_maglev.rb
       RUBY_VERSION:  1.8.7
       RUBY_ENGINE:   maglev
       Hello from MagLev

Then go play with the
[examples](https://github.com/MagLev/maglev/tree/master/examples) and read
the [further docs](/docs/further_docs.html)
