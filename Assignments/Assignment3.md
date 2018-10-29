Name: Christopher McArthur   ID: 40004257

# COMP 326/5261 - Assignment 3 - Fall 2018

Issued: October 8, 2018; Due: October 22, 2018 - Typed hardcopy in class

### 1. Loop Unrolling [44 marks]
Consider the following loop:
```
loop: l.d   f4,0(r1)   l1  
      l.d   f6,0(r2)   l2  
      mul.d f4,f4,f0   m1  
      mul.d f6,f6,f2   m2  
      add.d f4,f4,f6   a1  
      s.d   f4,0(r1)   s1  
      subi  r1,r1,8    sub1
      subi  r2,r2,8    sub2
      bnez  r1,loop    br
```
Note: Our convention is that FP arithmetics have 4 x-boxes.

Instruction | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 |`8`| 9 | 0 | 1 | 2 | 3
-----------:|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---
l1          | f | d | x | m | w |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |
l2          |   | f | d | x | m | w |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |
m1          |   |   | f | d | x | x | x | x | m | w |   |   |   |   |   |   |   |   |   |   |   |   |
m2          |   |   |   | f | d | s | s | s | x | x | x | x | m | w |   |   |   |   |   |   |   |   |
a1          |   |   |   |   | f | s | s | s | d | s | s | s | x | m | w |   |   |   |   |   |   |   |
s1          |   |   |   |   |   |   |   |   | f | s | s | s | d | x | m | w |   |   |   |   |   |   |
sub1        |   |   |   |   |   |   |   |   |   |   |   |   | f | d | s | s | x | m | w |   |   |   |
sub2        |   |   |   |   |   |   |   |   |   |   |   |   |   | f | s | s | d | x | m | w |   |   |
br          |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   | f |_d_| x | m | w |   |
l1          |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   | f | f | d | x | m | w 

a) [6 marks] Using the names 'l1' to 's1' for the first six instructions
in the body of this loop, draw the flow-dependence graph for just these
instructions.  Label each arrow with the dependence gap between the
producer and the consumer.

###### Answers:
```
┌──────────────────────────────────────┐
│    ┌───                      ───┐    |
│    | l1 ---> m1 --->            |    |
└──> |     1       4   a1 ---> s1 | O ─┘
     | l2 ---> m2 --->     1      |
     └───                      ───┘  
```

In what follows, focus on three flow-dependence types: i) FP arith to
FP arith, ii) FP arith to FP store, and iii) FP load to FP arith.  Denote
the number of m-boxes in memory references by '#m', and the number of
x-boxes in FP arithmetics by '#x'.

b) [6 marks] For each of the three designated flow-dependence types,
indicate the number of stalls in adjacent producer-consumer pairs as
functions of '#m' and '#x'.

###### Answers:
```
i)   FP arith to FP arith: 3 * #x
ii)  FP arith to FP store: 2 * #x
iii) FP load  to FP arith: 1 * #m
```

c) [10 marks] Suppose #m = 1 and #x = 4.  How many stalls occur in one
iteration of the loop if it is executed exactly as written?

###### Answers:
```
Number of Stalls: 8 

FP arith --> FP arith // x2
FP arith --> FP store // X1
```

d) [10 marks] Unroll the loop twice.  If one reschedules the unrolled
loop optimally, how many stalls are left?  (Keep the branch as the last
instruction, but feel free to pull up any 'subi' instructions for gap
padding.  Show the rescheduled code using the _short_ names.)

###### Answers:
```
Augmented Short Names:  ${instrcution}${iteration number}${instruction id}
l.d   f4,0(r1)   l11      l21
l.d   f6,0(r2)   l12      l22
mul.d f4,f4,f0   m11      m21
mul.d f6,f6,f2   m12      m22
add.d f4,f4,f6   a11      a21
s.d   f4,0(r1)   s11      s21
subi  r1,r1,8    sub11    sub21
subi  r2,r2,8    sub12    sub22
bnez  r1,loop    br

l11 --- >  m11   -->
l21 --- >  m21   -->  sub12 -->  a11  -->  s11  -->
l12 --- >  m12   -->  sub11 -->
l22 --- >  m22   -->  sub22 -->  a21  -->  s21  -->  __ STALL x2 Cycles __ sub21 -->  br
 ```

Instruction | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 0 | . | . | . | . | . | . | . | . | . | . | . | . 
-----------:|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---
l11         | f | d | x | m | w |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |
l12         |   | f | d | x | m | w |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |
m11         |   |   | f | d | x | x | x | x | m | w |   |   |   |   |   |   |   |   |   |   |   |
m12         |   |   |   | f | d | x | x | x | x | m | w |   |   |   |   |   |   |   |   |   |   |
l21         |   |   |   |   | f | d | x | m | w |   |   |   |   |   |   |   |   |   |   |   |   |
l22         |   |   |   |   |   | f | d | x | m | w |   |   |   |   |   |   |   |   |   |   |   |
m21         |   |   |   |   |   |   | f | d | x | x | x | x | m | w |   |   |   |   |   |   |   |
m22         |   |   |   |   |   |   |   | f | d | x | x | x | x | m | w |   |   |   |   |   |   |
sub12       |   |   |   |   |   |   |   |   | f | d | x | m | w |   |   |   |   |   |   |   |   |
add11       |   |   |   |   |   |   |   |   |   | f | d | x | m | w |   |   |   |   |   |   |   |
...         |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |
s11         |   |   |   |   |   |   |   |   |   |   | f | d | x | m | w |   |   |   |   |   |   |
sub11       |   |   |   |   |   |   |   |   |   |   |   | f | d | s | s | x | m | w |   |   |   |
br          |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   | f |_d_| x | m | w |   |
l11         |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   | f | f | d | x | m | w 

e) [8 marks] Five instructions in the original code contain immediates.
After unrolling the loop twice, some immediates may change.  Show all
instructions with correct immediates in the unrolled code.

###### Answers:
```
immediates was never refered too in class but I am assuming this is about registar names.
```

f) [4 marks] According to the strict definitions of data dependences,
there would be name dependences in the unrolled loop in the absence of
register renaming.  Do you think register renaming is really required?
Explain.

###### Answers:
```

```

### 2. Dynamic Instruction Scheduling I [34 marks]

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

###### Answers:
```

```

b) [12 marks] After both loads of the second iteration complete, but no
other action has taken place, show the contents of reservation stations
[7] through [12].  Mark any free reservation stations as 'free'.  Mark any
issuable instructions as 'issuable'.

###### Answers:
```

```

c) [10 marks] At this point, the d-box begins to dispatch the third
iteration.  When he has dispatched as much as possible, show the contents
of the reservation stations [7] through [12].  No other action takes place
unless I so indicate.

###### Answers:
```

```

### 3. Dynamic Instruction Scheduling II [22 marks]

a) [8 marks] There is a flow dependence from instruction 'alpha' to
instruction 'beta'.  The station containing 'alpha' is hotheaded and
divorces its functional unit before the latter completes.  How might
this violate the flow dependence?

###### Answers:
```

```

b) [8 marks] Three instructions---a producer, a consumer, and one that
writes to the p/c operand are dispatched in this order.  If producer
impersonation is impossible, why can't the third instruction harm the
operand?

###### Answers:
```

```

c) [6 marks] Three instructions are pairwise output-dependent.  All three
are dispatched before any one completes.  How many updates does the common
destination register receive? ___ Explain.

###### Answers:
```

```
