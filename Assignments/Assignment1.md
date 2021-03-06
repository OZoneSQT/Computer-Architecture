Name: Christopher McArthur   ID: 40004257

# COMP 326/5261 - Assignment 1 - Fall 2018

<h4 align='left'>Issued: September 17, 2018</h4>     <h4 align='right'>Due: October 1, 2018</h4>
                                                     <h4 align='right'>Typed hardcopy in class</h4>

### 1. Amdahl and Multicore [16 marks]

a) Gene Amdahl once observed, the less parallel portions of a program may 
limit parallelism gains from the more parallel portions.

Portion A of program P uses 3% of the sequential run time but gets no
speedup on a 128-core multicore die.  Portion B of program P uses 97% of the
sequential run time and gets a speedup equal to the number of cores on which
it runs.  We find that, to run portion A of program P twice as fast, we need
to _cannibalize_ the resources of 20 of the original cores to build one
larger core, leaving 108 original cores.  The plan: run A on the big core;
run B on the set of smaller cores.

Calculate the speedup with i) 128 original cores, and ii) one big core and 
108 original cores.  Give the two run times as a decimal fraction of the 
sequential run times and then calculate the speedups.

###### Answers:
```
i)  Ts = 3 + 97 = 100
    Tp-128 = 3 + ( 97 / 128 ) = 3.7578125
    S = Ts / Tp-128 = 100 / 3.7578125 = 26.6112266112
ii) Ts = 3 + 97 = 100
    Tp-108 = ( 3 / 2 ) + ( 97 / 108 ) = 2.39814814815
    S = Ts / Tp-108 = 100 / 2.39814814815 = 41.6988416988
```

b) Portion A of program P takes 'x' seconds; portion B takes 'y' seconds.
'x + y = 100'.  A is wholly sequential, while B is wholly parallel.  With
an infinite number of processors, we get a speedup of, say, 's'.  It takes
7 1/3 imaginary processors (8 real processors) to achieve a speedup of at
least 's/2'.  What are the values of 'x' and 'y'?  (They are integers).

###### Answer:
```
Ts = x + y = 100 ==> y = 100 - x

Tp-inif = x + ( y / INIFITY ) = x + ( ( 100 - x) / INIFITY )
S-inif = 100 / ( x + ( ( 100 - x ) / INIFITY ) ) = s

Tp-7.3 = x + ( y / 7.3333 ) = x + ( ( 100 - x) / 7.3333 ) 
S-7.3 = 100 / ( x + ( ( 100 - x ) / 7.3333 ) ) = s / 2

S-inif == 2(S-7.3)
100 / ( x + ( ( 100 - x ) / INIFITY ) ) == 2( 100 / ( x + ( ( 100 - x ) / 7.3333 ) ) )
100 / ( 2x + 2( ( 100 - x ) / INIFITY ) ) == 100 / ( x + ( ( 100 - x ) / 7.3333 ) )
100 * ( x + ( ( 100 - x ) / 7.3333 ) ) / ( 2x + 2( ( 100 - x ) / INIFITY ) ) * 100 == 1
( x + ( ( 100 - x ) / 7.3333 ) ) / ( 2x + 2( ( 100 - x ) / INIFITY ) ) == 1
x + ( ( 100 - x ) / 7.3333 ) ==  2x + 2( ( 100 - x ) / INIFITY )
( 100 - x ) / 7.3333 ==  2x + 2( ( 100 - x ) / INIFITY ) - x
100 - x ==  ( 2x + 2( ( 100 - x ) / INIFITY ) - x ) * 7.3333
100 - x ==  14.6666x + 14.6666( ( 100 - x ) / INIFITY ) - 7.3333x
100 - x ==  7.3333x + 14.6666( ( 100 - x ) / INIFITY )
100 ==  8.3333x + 14.6666( ( 100 - x ) / INIFITY )
100 ==  8.3333x + ( 14.6666( 100 - x ) / INIFITY )
100 ==  8.3333x + ( ( 1466.66 - 14.6666x ) / INIFITY )
100 ==  8.3333x + ( 1466.66 / INIFITY ) - ( 14.6666x / INIFITY )
100 ==  8.3333x + ( 0 ) - ( 0 ) // Limit
100 / 8.3333 == x
12 == x
y = 100 - x == 100 - 12 == 88


x = 12
y = 88
```

### 2. Power and Performance I [24 marks]

There are two options to scale a multicore-die's peak performance (ops/s) by
a _multiplicative_ factor of sigma: i) scale the number of cores by sigma,
or ii) scale the clock frequency by sigma (the voltage will scale by the
same amount).

In case  i), the power will scale by sigma.
In case ii), the power will scale---let us say---by sigma^2.75.

We can scale clock frequency up or down.  Or, we can increase or decrease
the number of (running) cores.

a) Show that core scaling is better, and clock scaling is worse, when sigma
\> 1.

###### Answer:
```
Power = W
Cores = n
Frequency = f
Voltage v

Core Scaling:
      W = n * sigma
Clock Scaling:
      W = ( v^2 * f ) * sigma^2.75
      
With core scaling in order to do quadruple the work we use 4x more power; however
to get quadruple the work done with clock scaling we must use ~45x the power when
power scaling is signma = 4.
```

b) Show that clock scaling is better, and core scaling is worse, when sigma
< 1.

###### Answer:
```
Power = W
Cores = n
Frequency = f
Voltage v

Core Scaling:
      W = n * sigma
Clock Scaling:
      W = ( v^2 * f ) * sigma^2.75
      
With core scaling in order to do quadruple the work we use 2x more power; however
to get quadruple the work done with clock scaling we must use ~1.2x the power
when power scaling is signma = 0.5.
```

c) If your multicore die is fully utilized and too hot, should you scale
down voltage and frequency or reduce the number of cores?  Explain.

###### Answer:
```
When the system is fully utilized it's power efficency will be reduced resulting
in a higher sigma coefficient; therefore scaling ( downwards ) the voltage and
frequence will have a greater impact on the heat being produced by the system.
```

### 3. Tolerating Work Latency [20 marks]

Consider a single-program-counter monocore CPU together with a GPU.  There
are two independent CPU threads, 't1' and 't2'.  't1' is a two-iteration
loop; it fills the GPU buffer with initial data, but cannot continue when
the buffer becomes full.  The GPU then runs, and signals---at the end of
its run---that the buffer is empty.  't2' does independent work lasting
850 ns.  One possible timeline---not showing 't2'---is:
```
  t1: 300 ns     g: 800 ns     t1: 300 ns     g: 800 ns    
|------------|---------------|------------|---------------|   (times shown)
```
A context switch is not free.  If thread 'a' blocks and later unblocks, the
'a' thread state must first be saved (250 ns) and later restored (250 ns).
By symmetry, the reverse is true of the thread 'b' to which thread 'a'
switches.  However, a _terminating_ thread has no need to have its state
saved.  Assume "scheduling" is free.

a) Suppose 't1' spin waits when the buffer becomes full.  Including 't2',
when does the program finish?

###### Answer:
```
  t1: 300 ns     g: 800 ns     t1: 300 ns     g: 800 ns       t2: 850ns  
|------------|---------------|------------|---------------|---------------|

Total Time: 3050 ns
```

b) Suppose context switches are free.  Including 't2', when does the program
finish?

###### Answer:
```
  t1: 300 ns     g: 800 ns     t1: 300 ns     g: 800 ns    
|------------|---------------|------------|---------------|
             |---------------|            |-----|
                 t2: 800 ns              t2: 50 ns

Total Time: 2200 ns
```

c) Suppose context switches are not free (see above).  Including 't2', when
does the program finish?

###### Answer:
```
  t1: 300 ns     g: 800 ns              t1: 300 ns     g: 800 ns    
|------------|---------------|       |------------|---------------|
             |-------|-------|-------|            |-------|-------|
         cs: 250 ns  t2: 550ns  cs: 250 ns      cs: 250 ns  t2: 300ns

Total Time: 2200 ns
```

d) Suppose context-switch cost doubles.  Including 't2', when does the
program finish?

###### Answer:
```
  t1: 300 ns     g: 800 ns                   t1: 300 ns     g: 800 ns    
|------------|---------------|             |------------|---------------|
             |----------|-------|----------|            |----------|------------|
            cs: 500 ns  t2: 300ns  cs: 500 ns            cs: 500 ns   t2: 550ns

Total Time: 2950 ns
```

### 4. The Memory-Latency Wall [15 marks]

Killer micros mitigate memory latency by having a multilevel cache reduce
the latency to a couple of cycles, and then using pipeline parallelism
(from ILP) to tolerate the rest.  This strategy would collapse if the cache
left any more than a few cycles of latency.  Why?  Because killer micros
have ridiculously little pipeline parallelism.

Let us calculate the average time 't*' to complete a memory reference
measured in processor cycles.  This will show the remaining latency.

Let 't_c' and 't_m' be the D-cache and DRAM access times, and let 'P' be the
probability of a D-cache hit.  The cache line is 1-word long.  We have:
```
  tav = P * t_c + (1 - P) * (t_m + t_c)  (in seconds)
      = t_c + (1 - P) * t_m              (in seconds)
```
Now, divide l.h.s. and r.h.s. by t_c (= 1 cycle), in seconds, to get:
```
  t*  = 1   + (1 - P) * t_m/t_c          (dimensionless; counts cycles)
```
In line 3, we have assumed that the "hit" time 't_c' is always one cycle.

Assume a D-cache with a miss rate of 1%.  Assume the DRAM latency decreases
by a factor of 1.05 every year, and the processor clock cycle decreases by a
factor of 1.45 every year.  If a memory reference has a 200-cycle latency
today, how many cycles will 't*' be after 6 years?  after 8?

###### Answer:
```
t* : average memory access time in cycles   = 1 + (1 - P) * t_m/t_c
t_c : D-cache access time         = 1
t_m : DRAM access time            = 200 / 1.05 * y   // Are you sure you meant decreases?
p  : D-cache hit probability      = 0.99
t*(y) : yearly decrease           = t* / 1.45 * y    // Are you sure you meant decreases?

t*(y) = ( 1 + (1 - P) * t_m/t_c ) / ( 1.45 * y )

t*(6) = 0.529 cycles
t*(8) = 0.451 cycles
```

### 5. The Memory-Bandwidth Wall [15 marks]

When a cache is used temporally, it acts as a bandwidth amplifier.
Specifically, the effective operand bandwidth reaching the pipeline is the
actual operand bandwidth reaching the processor divided by the miss rate.

Suppose we must deliver an actual operand bandwidth of 'n' words/second to
the processor to sustain a floating-point performance of 'n/m' flops/second,
where 'm' is the miss rate of the D-cache.  Typically, we need a new word
from either the cache or the memory for each flop.  By assumption, 'm' is
always 0.01.  Today, the actual operand bandwidth delivered to the processor
is 6 * 10^7 words/second and the peak floating-point performance is 2 * 10^9
flops/second.  If peak floating-point performance increases by a factor of
1.65 every year and memory bandwidth increases by a factor of 1.25 every year,
after how many years will the memory bandwidth be _just sufficient_ to sustain
a floating-point performance equal to the peak performance?

###### Answer:
```
operand bandwidth : n ( words/second )              = 6 * 10^7
D-cache miss rate : m ( ??? )                       = 0.01
floating-point performance : n/m ( flops/second )   = 2 * 10^9

ob(y) = (6 * 10^7) * 1.25 * y + (6 * 10^7)
fp(y) = (2 * 10^9) * 1.65 * y + (2 * 10^9)


ob(y)/m == fp(y)
( (6 * 10^7) * 1.25 * y + (6 * 10^7) ) / 0.01 == (2 * 10^9) * 1.65 * y + (2 * 10^9)
(6 * 10^7) * 1.25 * y + (6 * 10^7) == (2 * 10^7) * 0.0165 * 0.01y + (2 * 10^7)
75000000y + 60000000 == 3300y + 20000000
74996700y == -40000000
y == -0.5334 // So never !

```
### 6. Multicore and the Memory Wall [10 marks]

Relative to each other, CPUs have few threads and enormous caches, while GPUs
have many threads and tiny caches.  Generally speaking, we need to think
carefully about any hardware architecture in which many hardware threads
share a cache (usually, but not always, the last-level cache).  GPUs are the
extreme example of such an architecture.

Suppose threads are cache friendly in the sense that each portion of a thread
accesses a small subset of memory, and that this subset changes relatively
slowly.  Yet threads are normally interleaved with some frequency, rather
than being allowed to run to completion.  The technical term for this slowly
changing subset of interesting data is the thread's _working set_.

Now suppose that the working set of a thread is roughly the same size as the
shared cache.  Further suppose that the hardware schedules threads much more
rapidly than the speed with which their working sets evolve.

As a thought experiment, in which case is a thread more likely to find its data
in cache:

Case i: We schedule many threads rapidly for short intervals, as described
above.

Case ii: We schedule one thread for a relatively long time so that it owns the
cache for an interval roughly equal to one phase of the evolution of its working
set.

Explain.

###### Answer:
```
The likelyhood of a cache hit in scenerio i depends on the data-dependency of the
threads. If all the threads are running on the same data (which fits in the cache)
then switching threads rapidly would yeild a high hit rate; however with scenario ii
and long thread execution, each thread switch would have a completely different
working set resulting in a terrible cache hit rate.

In contrast with a small data-dependency between threads where each thread is
performing longand complex computations on a unique data set, only then would we see
longer thread execution times yeilding higher cache hit rates the the secenerio i with
rapid thread which.

In most other circumstance the cache hit rate between these two scenerios would be on
average the same.
```
