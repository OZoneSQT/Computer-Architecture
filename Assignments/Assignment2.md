Name: Christopher McArthur   ID: 40004257

# COMP 326/5261 - Assignment 2 - Fall 2018

<h4 align='left'>Issued: October 1, 2018</h4>     <h4 align='right'>Due: October 15, 2018</h4>
                                                  <h4 align='right'>(No class, October 8)</h4>

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
???
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
???
```

### 2. In-Order (Linear) Pipelines [25 marks]
```
         +-+           +-+           +-+           +-+
<f-box>  | |  <d-box>  | |  <x-box>  | |  <m-box>  | |  <w-box>
         +-+           +-+           +-+           +-+
         f/d           d/x           x/m           m/w
```
##### loop:
instruction | registers
:-----------|:---
lw          | r1,0(r2)
addi        | r1,r1,1
sw          | r1,0(r2)
addi        | r2,r2,4
sub         | r4,r3,r2
bnez        | r4,loop

a) [5 marks] Is their one data dependence in this code that is mediated
through a memory location rather than through a register?  ___ (yes/no)
Explain, specifying type if the answer is "yes".

b) [5 marks] Draw the space-time diagram for the first iteration of this
loop.

c) [5 marks] In the 5-stage pipeline, the longest stage requires 0.8 ns
for its compute portion, and 0.1 ns for its write-back portion.  What is
the clock-cycle time of the 5-stage pipeline?  If a 10-stage pipeline is
produced by splitting all stages in half, what is the cycle time of the
10-stage pipeline?  Note: you cannot split latches.

d) [10 marks] The 10-stage pipeline has some subtleties.  First, each pair
of new boxes is a mini pipeline.  A mini pipeline must finish computing
its result before forwarding the answer to some other mini pipeline.
Second, if the mini pipeline is <a1,a2>, a1 is a hardware device that
cannot be doing two things at once.  Draw the space-time diagram for the
first iteration of this loop executing on the 10-stage pipeline.

### 3. Loop Timing I [15 marks]

Consider the following code fragment:

##### loop:
instruction | registers
:-----------|:---
lw          | r1,0(r3)
lw          | r2,0(r4)
add         | r1,r1,r2
sw          | 0(r3),r1
addi        | r3,r3,4
addi        | r4,r4,4
sub         | r6,r5,r3
bnez        | r6,loop

The loop iterates 90 times.  Draw a space-time diagram of this code.
Calculate the total execution time of the loop.

### 4. [20 marks] Floating-Point Instructions (Loop Timing II)

Consider the following code fragment:

##### loop:
instruction | registers
:-----------|:---
l.d         | f0,0(r2)
l.d         | f2,0(r3)
s.d         | f0,0(r2)
sub.d       | f4,f4,f0
add.d       | f4,f4,f4
addi        | r2,r2,#8
addi        | r3,r3,#8
sub         | r4,r5,r3
bnez        | r4,loop

'sub.d' and 'add.d' have 3 x-boxes.  'mul.d' has 4 x-boxes.  Functional
units are fully pipelined.  The loop iterates 90 times.  Draw the
space-time diagram of this code.  Calculate the total execution time of
the loop.

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
```
                1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 0 | 1 | 2 | 3 
l.d   f0,0(r1)  f | d | xi| m1| m2| w |   |   |   |   |   |   |   
               ---------------------------------------------------
l.d   f2,0(r2)    | f | d | xi| m1| m2| w |   |   |   |   |   |   
               ---------------------------------------------------
mul.d f4,f0,f2    |   | f | d | s | s | x1| x2| x3| x4| n | w |   
               ---------------------------------------------------
s.d   f4,0(r1)    |   |   | f | s | s | d | xi| s | s | m1| m2|   
```
a) [5 marks] How does 'f0' get to "the" x-box of 'mul.d'?  Mention box and
latch names and precise timings.  (I guess listing _every_ latch in f0's
passage would be unambiguous, but I want timings, i.e., cycles).

b) [5 marks] How does 'f2' get to "the" x-box of 'mul.d'?  Mention box and
latch names and precise timings.  (I guess listing _every_ latch in f2's
passage would be unambiguous, but I want timings, i.e., cycles and half-cycles).

c) [5 marks] Are there structural hazards in this diagram?  ___ (yes/no) Explain. 

d) [5 marks] If the 's.d' became an 'l.d', how would this change the data
dependences?
