# Chapter 3 Summary

### Loop Unrolling
Results in clumping all the loads and adds fro mthe various iteration to introduce padding between the dependencies.

Register renaming is the key to success.

- Flow depenency
Producer consumer dynamic
```c
int x = 1;
// ...
y = x & 0x000F;
```

- Output dependency
Switching order invalidates data.
```c
int isTrue = 1;
isTrue = ( y | z );
```

- Anti dependency
Must be consumed before re-use
```c
int y = 2 * x + 3;
// ...
x = 2;
```

###### Gap/Latency Table 
Certain types of instruction that follow each other impose a number of stalls when faced with a flow dependency

Loop Unrolling is static schedualing. ( RISC 1.0 Overlapping )
- does not handle dynamic runtime latency
- key function is register renaming to remove output and anti dependencies. _Note_: register names use even numbers

Dynamic schedualing - out of order pipelineing ( RISC 2.0 Permuting )
- no linear order
- follows graph branches
- changes when branch delays ( stalls )

Feasable due to increases in memory capacity of CPUs.

### Loop timing
Given loop segment, write out pipeline per instruction at each cycle, decode of branch instruction ( last in loop ) is the loop's gap. Gap * N - 1 iterations + max(cycle@instruction) = loop's runtime.

### RISC 2.0 

Modify { f, d, x, m, w } pipeline --> { f, d, rs(alu), m, w }
- decode now _dispatches_ in program order to **reservation station**
- RS issues instructions by "marrying" them to an ALU when left and right operands are values.

> Rules ensure dependencies are no broken. no imperssionation is the key.

Speculative Execution: Execute next _predicated branch_ before it's even been confirmed!
