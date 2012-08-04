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

MagLev itself (the Ruby & Smalltalk source released on Github) is free. GemStone/S is licensed based on resources. The free version below is perfectly sufficient for small to medium sized projects.

<table id="matrix">
  <thead>
    <tr>
      <th></th>
      <th colspan="3">GemStone/S 64-bit</th>
    </tr>
  </thead>
  <tbody>
    <tr class="odd">
      <td>Price</td>
      <td>Free</td>
      <td>$7k/core</td>
      <td>$15k/core</td>
    </tr>
    <tr>
      <td>Maxiumum Cores Used</td>
      <td>2</td>
      <td>4</td>
      <td>8</td>
    </tr>
    <tr class="odd">
      <td>Maximum RAM<br />(Shared Page Cache Size)</td>
      <td>2GB</td>
      <td>2GB</td>
      <td>4GB</td>
    </tr>
    <tr>
      <td>Maximum Disk<br />(Repository Size)</td>
      <td colspan="3">Unlimited</td>
    </tr>
    <tr class="odd">
      <td>Clustered Servers<br />(Remote Gems)</td>
      <td colspan="3">No</td>
    </tr>
    <tr>
      <td>Linux/OS X</td>
      <td colspan="3">Yes</td>
    </tr>
    <tr class="odd">
      <td>Windows/Solaris/Aix/HP-UX</td>
      <td colspan="3">No</td>
    </tr>
    <tr>
      <td>Support</td>
      <td>Community</td>
      <td>Contact</td>
      <td>Contact</td>
    </tr>
  </tbody>
</table>
