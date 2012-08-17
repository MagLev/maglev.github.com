---
title: Report an Issue with MagLev
layout: default
---
# MagLev Issue Reports

The MagLev team uses the [GitHub issue
tracker](https://github.com/MagLev/maglev/issues), where you can view and
report issues.

To help us better deal with the issue, please include:

1. The version of MagLev

       $ maglev-ruby -v
       maglev 1.0.0 (ruby 1.8.7) (2011-10-31 rev 1.0.0-27184)[Darwin i386]

2. Information about the system you are running on:

       $ uname -a
       Darwin miami.local 10.8.0 Darwin Kernel Version 10.8.0: Tue Jun  7 16:33:36 PDT 2011; root:xnu-1504.15.3~1/RELEASE_I386 i386


3. Whether you installed using RVM or from GitHub.

## Stack Traces

If possible, a stack trace of where the issue occurs is always nice to
have.

To generate a stack trace:

1. Run MagLev with the debug option.

       $ export MAGLEV_OPTS="-d"
       $ maglev-ruby the_script_that_breaks.rb

1. When the error occurs, MagLev will stop in the debugger, indicated by
   the `topaz 1> ` prompt:

       error , a ZeroDivide occurred (error 2026), reason:numErrIntDivisionByZero, attempt to divide 1 by zero,
                    during /Users/pmclain/GemStone/checkouts/the_script_that_breaks.rb
       ERROR 2026 , a ZeroDivide occurred (error 2026), reason:numErrIntDivisionByZero, attempt to divide 1 by zero (ZeroDivisionError)
       topaz 1> exitifnoerror
       End of initialization files
       topaz 1>

1. Tell debugger to print stack trace

   First tell topaz to copy output to a file `output push
   <some_file_name>`, then print the stack trace with the `stack` command:

       topaz 1> output push maglev_stack.txt
       topaz 1> stack
       ==> 1 AbstractException >> _outer:with:        (envId 0) @8 line 19
           receiver [114893057 sz:10  ZeroDivide] a ZeroDivide occurred (error 2026), reason:numErrIntDivision...
           aHandler nil
           isNonStaticAnsi nil
       2 AbstractException >> outer               (envId 0) @2 line 19
           receiver [114893057 sz:10  ZeroDivide] a ZeroDivide occurred (error 2026), reason:numErrIntDivision...
       3 [] in  RubyCompiler >> compileFileNamed:loadName:env: (envId 0) @4 line 58
           self [114898433  RubyCompiler] aRubyCompiler
           receiver [114898945 sz:0  ExecBlock1] anExecBlock1
           ex [114893057 sz:10  ZeroDivide] a ZeroDivide occurred (error 2026), reason:numErrIntDivision...
           prevSelf nil

       ...more stack trace...

1. You can type exit to quit the MagLev VM:

       topaz 1> exit

   The stack trace is captured in `maglev_stack.txt` (or whatever you named
   the file).

