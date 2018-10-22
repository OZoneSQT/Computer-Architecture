Name: Christopher McArthur   ID: 40004257

# COMP 326/5261 - Assignment 2 Revised - Fall 2018

Issued: October 1, 2018, Due: October 15, 2018 (No class, October 8)

Corrected: October 19, 2018, Due: October 22, 2018 
 
### 1. Special-Purpose Superscalar [20 marks]

Assumptions: i) single-cycle pipelining, ii) 5-cycle instruction latency, and
iii) five pipeline stages.  We build a four-lane RISC 1.0 pipeline by laying
down four one-lane 'fdxmw' pipelines parallel to each other.  This gives a
concurrency of 5 in time and a concurrency of 4 in space.

a) [5 marks] {Pi} is a restricted class of programs.  Every member Pj of the
group can be decomposed into four independent (million-instruction) threads
(these threads do not communicate or synchronize with one another).  Assuming
no stalls, what is the speedup of the four-lane 'fdxmw' pipeline over the
one-lane, unpipelined 'fdxmw' datapath?  Show work.

###### Answers:
```
Ts = 100 // Sequental Time === unpipelined
Tp = Ts / ( Pipline-width * Pipeline-lanes ) = 100 / ( 5 * 4 ) = 5

S = Ts / Tp = 100 / 5 = 20
```

b) [5 marks] By what factor must fetch bandwidth be increased for the
four-lane machine?  By what factor must result bandwidth be increased
assuming each result is written to memory?  Explain in a few words.

###### Answers:
```
When moving from a sequential lane to a four lane pipeline the fetch bandwidth
must be multiplied by a factor of four in order to support the increased input
rate of the four lane pipeline going from one instruction a cycle to four per
cycle. At full equilibrium the result Bandwidth must be multiplied by a factor
of 20 to support the increase volume and speed of output values.
```

c) [5 marks] Of course, there are _intrathread_ stalls.  Executing program P4
shows the following average number of stalls per instruction in each lane:
<0.15, 0.20, 0.10, 0.25>.  What is this more realistic speedup on P4?  Show
work.

###### Answers:
```
Ts = 100 // Sequental Time === unpipelined
Tp = Sigma[ ( Ts / Pipline-width ) * ( 1 - Stalls/instruction ) ]
   = ( 100 /  5 ) * ( 1 - 0.15 ) +
     ( 100 /  5 ) * ( 1 - 0.20 ) +
     ( 100 /  5 ) * ( 1 - 0.10 ) +
     ( 100 /  5 ) * ( 1 - 0.25 ) +
   = ( 4 * 0.85 ) + ( 4 * 0.80 ) + ( 4 * 0.90 ) + ( 4 * 0.75 )
   = 13.2
     
S = Ts / Tp = 100 / 13.2 = ~7.58
```

d) [5 marks] {Qi} is a restricted class of programs.  Every member Qj of the
group can be decomposed into four _dependent_ (million-instruction) threads
(these threads do communicate and/or synchronize with one another).  Unlike
cores, boxes can not be turned on and off.  How does the power efficiency of
running one of the Qj compare to running one of the Pj?  Explain.

###### Answers:
```
The power efficiency of Qj will be much worse than that of Pj because multiple 
boxes will be experiencing stalls while waiting on other boxes meaning nothing
else can run in that cycle crippling the efficiency.
```

### 2. In-Order (Linear) Pipelines [25 marks]
```
         +-+           +-+           +-+           +-+
<f-box>  | |  <d-box>  | |  <x-box>  | |  <m-box>  | |  <w-box>
         +-+           +-+           +-+           +-+
         f/d           d/x           x/m           m/w

loop: lw    r1,0(r2)
      addi  r1,r1,1
      sw    r1,0(r2)
      addi  r2,r2,4
      sub   r4,r3,r2
      bnez  r4,loop
```

a) [5 marks] Is their one data dependence in this code that is mediated
through a memory location rather than through a register?  ___ (yes/no)
Explain, specifying type if the answer is "yes".

###### Answers:
```
There are many data dependencies between the instruction, flow data and anti-
dependencies are all present in this example. However there apeares to only be
registers being used in this example so I can not venture to say if they are
being mediated by a memory location, certainly from a memory access is there
mediation but I am no confident with your choice of words for this question.
```

b) [5 marks] Draw the space-time diagram for the first iteration of this
loop.

###### Answer
instr | register | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7
:-----|:--------:|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---
lw    | r1,0(r2) | f | d | x | m | w |   |   |   |   |   |   |   |   |   |   |   |
addi  | r1,r1,1  |   | f | d | s | x | m | w |   |   |   |   |   |   |   |   |   |
sw    | r1,0(r2) |   |   | f | s | d | x | n | w |   |   |   |   |   |   |   |   |
addi  | r2,r2,4  |   |   |   | s | f | d | s | s | x | m | w |   |   |   |   |   |
sub   | r4,r3,r2 |   |   |   |   |   | f | s | s | d | s | s | x | m | w |   |   |
bnez  | >r4,loop |   |   |   |   |   |   | s | s | f | s | s | s | d | x | m | w |
lw    | r1,0(r2) |   |   |   |   |   |   | s | s | f | s | s | s | f | d | x | m | w

c) [5 marks] In the 5-stage pipeline, the longest stage requires 0.8 ns
for its compute portion, and 0.1 ns for its write-back portion.  What is
the clock-cycle time of the 5-stage pipeline?  If a 10-stage pipeline is
produced by splitting all stages in half, what is the cycle time of the
10-stage pipeline?  Note: you cannot split latches.

###### Answers:
```

How do we account for the time of the other 3 box types???

```

d) [10 marks] The 10-stage pipeline has some subtleties.  First, each pair
of new boxes is a mini pipeline.  A mini pipeline must finish computing
its result before forwarding the answer to some other mini pipeline.
Second, if the mini pipeline is <a1,a2>, a1 is a hardware device that
cannot be doing two things at once.  Draw the space-time diagram for the
first iteration of this loop executing on the 10-stage pipeline.

###### Answer
instr | register | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7
:-----|:--------:|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---
lw    | r1,0(r2) |f1 |f2 |d1 |d2 |x1 |x2 |m1 |m2 |w1 |w2 |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |
addi  | r1,r1,1  |   |f1 |f2 |d1 |d2 | s | s | s |x1 |x2 |m1 |m2 |w1 |w2 |   |   |   |   |   |   |   |   |   |   |   |   |
sw    | r1,0(r2) |   |   |f1 |f2 |d1 | s | s | s |d2 | s |x1 |x2 |n1 |n2 |w1 |w2 |   |   |   |   |   |   |   |   |   |   |
addi  | r2,r2,4  |   |   |   |f1 |f2 | s | s | s |d1 | s |d2 | s | s | s | s | s |x1 |x2 |m1 |m2 |w1 |w2 |   |   |   |   |
sub   | r4,r3,r2 |   |   |   |   |f1 | s | s | s |f2 | s |d1 | s | s | s | s | s |d2 | s |x1 |x2 |m1 |m2 |w1 |w2 |   |   |
bnez  | >r4,loop |   |   |   |   |   | s | s | s |f1 | s |f2 | s | s | s | s | s |d1 | s |d2 | s |x1 |x2 |m1 |m2 |w1 |w2 |
lw    | r1,0(r2) |   |   |   |   |   |   | s | s |   | s |f1 | s | s | s | s | s |f2 | s |f1 | s |f2 |d1 |d2 |x1 |x2 |m1 |m2 

### 3. Loop Timing I [15 marks]

Consider the following code fragment:
```
loop: lw   r1,0(r3)
      lw   r2,0(r4)
      add  r1,r1,r2
      sw   0(r3),r1
      addi r3,r3,4
      addi r4,r4,4
      sub  r6,r5,r3
      bnez r6,loop
```

The loop iterates 90 times.  Draw a space-time diagram of this code.
Calculate the total execution time of the loop.

###### Answer
instr | register | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 0 | 1 | 2 |`3`| 4 | 5 | 6 | 7 | 8
:-----|:--------:|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---
lw    | r1,0(r3) | f | d | x | m | w |   |   |   |   |   |   |   |   |   |   |   |   |
lw    | r2,0(r4) |   | f | d | x | m | w |   |   |   |   |   |   |   |   |   |   |   |
add   | r1,r1,r2 |   |   | f | d | s | x | m | w |   |   |   |   |   |   |   |   |   |
sw    | 0(r3),r1 |   |   |   | f | s | d | x | n | w |   |   |   |   |   |   |   |   |
addi  | r3,r3,4  |   |   |   |   | s | f | d | s | s | x | m | w |   |   |   |   |   |
addi  | r4,r4,4  |   |   |   |   |   |   | f | s | s | d | x | m | w |   |   |   |   |
sub   | r6,r5,r3 |   |   |   |   |   |   |   | s | s | f | d | x | m | w |   |   |   |
bnez  | r6,loop  |   |   |   |   |   |   |   |   |   |   | f | s | d | x | m | w |   |
lw    | r1,0(r3) |   |   |   |   |   |   |   |   |   |   |   | s | f | f | d | x | m | w 

```
Te = Loop-Duration * ( N - 1 ) + Program-Cycle-Time = ( 13 * 89 ) + 18 = 1175
```

### 4. [20 marks] Floating-Point Instructions (Loop Timing II)

Consider the following code fragment:
```
loop: l.d   f0,0(r2)
      l.d   f2,0(r3)
      mul.d f4,f0,f2
      s.d   f0,0(r2)
      sub.d f4,f4,f0
      add.d f4,f4,f4
      addi  r2,r2,8
      addi  r3,r3,8
      sub   r4,r5,r3
      bnez  r4,loop
```

'sub.d' and 'add.d' have 3 x-boxes.  'mul.d' has 4 x-boxes.  Functional
units are fully pipelined.  The loop iterates 90 times.  Draw the
space-time diagram of this code.  Calculate the total execution time of
the loop.

###### Answer
instr | register | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 0 | 1 | 2 | 3 | 4 |`5`| 6 | 7 | 8 | 9 | 0
:-----|:--------:|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---
l.d   | f0,0(r2) | f | d | x | m | w |   |   |   |   |   |   |   |   |   |   |   |   |   |   |
l.d   | f2,0(r3) |   | f | d | x | m | w |   |   |   |   |   |   |   |   |   |   |   |   |   |
mul.d | f4,f0,f2 |   |   | f | d | s | x | x | x | x | m | w |   |   |   |   |   |   |   |   |
s.d   | f0,0(r2) |   |   |   | f | s | d | x | m | w |   |   |   |   |   |   |   |   |   |   |
sub.d | f4,f4,f0 |   |   |   |   |   | f | d | x | x | x | m | w |   |   |   |   |   |   |   |
add.d | f4,f4,f4 |   |   |   |   |   |   | f | d | s | s | x | x | x | m | w |   |   |   |   |
addi  | r2,r2,#8 |   |   |   |   |   |   |   | f | s | s | d | x | m | w |   |   |   |   |   |
addi  | r3,r3,#8 |   |   |   |   |   |   |   |   |   |   | f | d | x | m | w |   |   |   |   |
subi  | r4,r5,r3 |   |   |   |   |   |   |   |   |   |   |   | f | d | x | m | w |   |   |   |
bnez  | r4,loop  |   |   |   |   |   |   |   |   |   |   |   |   | f | s | d | x | m | w |   |
l.d   | f0,0(r2) |   |   |   |   |   |   |   |   |   |   |   |   |   | s | f | f | d | x | m | w

```
Te = Loop-Duration * ( N - 1 ) + Program-Cycle-Time =  15 * ( 90 - 1  ) + 20 = 1355
```

### 5. Pipeline Boxes and Pipeline Latches [20 marks]
```
+-------------------------------------------------------------------------+ P
|     <I-cache>               <Register file>               <D-cache>     | r
|                                                                         | o
|                             <Control circuitry>                         | c
|              +-+           +-+           +-+           +-+              | e
|     <f-box>  | |  <d-box>  | |  <x-box>  | |  <m-box>  | |  <w-box>     | s
|              +-+           +-+           +-+           +-+              | s
|              f/d           d/x           x/m           m/w              | o
+-------------------------------------------------------------------------+ r
```
Memrefs have 2 m-boxes, and floating-point multiplies have 4 x-boxes.  Integer
add is denoted xi.  Consider the following space-time diagram:

instr | register | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 0 | 1 | 2 
:-----|:--------:|---|---|---|---|---|---|---|---|---|---|---|---
l.d   | f0,0(r1) | f | d | xi| m1| m2| w |   |   |   |   |   |   
l.d   | f2,0(r2) |   | f | d | xi| m1| m2| w |   |   |   |   |   
mul.d | f4,f0,f2 |   |   | f | d | s | s | x1| x2| x3| x4| n | w 
s.d   | f4,0(r1) |   |   |   | f | s | s | d | xi| s | s | m1| m2

a) [5 marks] How does 'f0' get to "the" x-box of 'mul.d'?  Mention latch
names and timings.  Syntax: "to ___ latch/register in (half-)cycle ___ ";
repeat this phrase as necessary.

###### Answer
```
0 - in register r1
1 - to m/w latch in (half-)cycle 5
2 - to f0 register in (half-)cycle 6
3 - to d/x latch in (half-)cycle 6
```

b) [5 marks] How does 'f2' get to "the" x-box of 'mul.d'?  Mention latch
names and timings.  Syntax: "to ___ latch/register in (half-)cycle ___ ";
repeat this phrase as necessary.

###### Answer
```
0 - in register r2
1 - to m/w latch in (half-)cycle 6
2 - to d/x latch in (half-)cycle 6
```

c) [5 marks] Are there structural hazards in this diagram?  ___ (yes/no)
Explain. 

###### Answer
```
Yes as the mul.d requires the result of the two l.d instructions in addition
to the s.d requiring the computation of the mul.d
```

d) [5 marks] If the 's.d' became an 'l.d', how would this change the data
dependences?

###### Answer
```
This would change the data dependency into an anti-dependency
```
