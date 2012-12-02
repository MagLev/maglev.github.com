---
layout: default
title: MagLev
---
{% include fork_me.html %}

# MagLev

MagLev is a fast, stable, 64-bit [open source](https://github.com/MagLev/maglev/blob/master/Licenses/README.txt)
implementation of the Ruby programming language and libraries built
on top of VMware's GemStone/S 3.1 Virtual Machine.

The MagLev VM takes full advantage of GemStone/S JIT to native code
performance, distributed shared cache, fully ACID transactions, and
enterprise class NoSQL data management capabilities to provide a
robust and durable programming platform. It can transparently manage
a much larger amount (terabytes) of data and code than will fit in memory.
There are no restrictions on what types of objects, classes, blocks,
threads or continuations that can be stored and executed.

MagLev 1.0.0 was released on October 31, 2011.

# Get Started

If you are interested in persistent distributed Ruby objects, [get started
with MagLev](/docs/get_started.html).

# Licensing & Pricing

MagLev itself (the Ruby & Smalltalk source released on Github) is free and [open source](https://github.com/MagLev/maglev/blob/master/Licenses/README.txt). 

To run MagLev you'll also need a GemStone/S Server. There is a [Web Edition of GemStone/S](http://seaside.gemstone.com/docs/GLASS-Pricing-1201.htm) (a.k.a GLASS) you may use at no charge to develop, deploy, and distribute MagLev applications, as long as you abide by the terms of the [License](http://seaside.gemstone.com/docs/GLASS-License.pdf) and the [resource limits](http://seaside.gemstone.com/docs/GLASS-Pricing-1201.htm) imposed by the included keyfile. This free version should be sufficient for most small to medium sized projects. 
You may purchase higher capacity [Web Editions of GemStone/S](http://seaside.gemstone.com/docs/GLASS-Pricing-1201.htm) if needed.
