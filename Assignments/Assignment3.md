COMP 326/5261                   Assignment 3                       Fall 2018

Issued: October 8, 2018                                Due: October 22, 2018
                                                     Typed hardcopy in class
1. Loop Unrolling [44 marks]

Consider the following loop:

loop: l.d   f4,0(r1)   l1  
      l.d   f6,0(r2)   l2  
      mul.d f4,f4,f0   m1  
      mul.d f6,f6,f2   m2  
      add.d f4,f4,f6   a1  
      s.d   f4,0(r1)   s1  
      subi  r1,r1,8    sub1
      subi  r2,r2,8    sub2
      bnez  r1,loop    br

Note: Our convention is that FP arithmetics have 4 x-boxes.  

a) [6 marks] Using the names 'l1' to 's1' for the first six instructions
in the body of this loop, draw the flow-dependence graph for just these
instructions.  Label each arrow with the dependence gap between the
producer and the consumer.

In what follows, focus on three flow-dependence types: i) FP arith to
FP arith, ii) FP arith to FP store, and iii) FP load to FP arith.  Denote
the number of m-boxes in memory references by '#m', and the number of
x-boxes in FP arithmetics by '#x'.

b) [6 marks] For each of the three designated flow-dependence types,
indicate the number of stalls in adjacent producer-consumer pairs as
functions of '#m' and '#x'.

c) [10 marks] Suppose #m = 1 and #x = 4.  How many stalls occur in one
iteration of the loop if it is executed exactly as written?

d) [10 marks] Unroll the loop twice.  If one reschedules the unrolled
loop optimally, how many stalls are left?  (Keep the branch as the last
instruction, but feel free to pull up any 'subi' instructions for gap
padding.  Show the rescheduled code using the _short_ names.)

e) [8 marks] Five instructions in the original code contain immediates.
After unrolling the loop twice, some immediates may change.  Show all
instructions with correct immediates in the unrolled code.

f) [4 marks] According to the strict definitions of data dependences,
there would be name dependences in the unrolled loop in the absence of
register renaming.  Do you think register renaming is really required?
Explain.

2. Dynamic Instruction Scheduling I [34 marks]

Imagine that reservation stations only track whether floating-point operands
are valid, and that integer operands appear by magic whenever needed, with
one exception, noted below.

a) [12 marks] Unroll the loop twice.  Dispatch the first 12 FP instructions
to the pipeline's 12 reservation stations, viz., [1] to [12].  We cheat on
the load RSs, so do not show those stations.  FP arithmetic is normal.  But
's.d' has two operands: the value stored, and the memory address.  Use the
notation 'ear' when you are listening, and 'val' when you are not.  A memory
FP value is written Mem[r5].  Indicate opcodes.  Show station contents for
[9] through [12].

b) [12 marks] After both loads of the second iteration complete, but no
other action has taken place, show the contents of reservation stations
[7] through [12].  Mark any free reservation stations as 'free'.  Mark any
issuable instructions as 'issuable'.

c) [10 marks] At this point, the d-box begins to dispatch the third
iteration.  When he has dispatched as much as possible, show the contents
of the reservation stations [7] through [12].  No other action takes place
unless I so indicate.

3. Dynamic Instruction Scheduling II [22 marks]

a) [8 marks] There is a flow dependence from instruction 'alpha' to
instruction 'beta'.  The station containing 'alpha' is hotheaded and
divorces its functional unit before the latter completes.  How might
this violate the flow dependence?

b) [8 marks] Three instructions---a producer, a consumer, and one that
writes to the p/c operand are dispatched in this order.  If producer
impersonation is impossible, why can't the third instruction harm the
operand?

c) [6 marks] Three instructions are pairwise output-dependent.  All three
are dispatched before any one completes.  How many updates does the common
destination register receive? ___ Explain.
