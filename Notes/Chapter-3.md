# Chapter 3 Summary

### Loop Unrolling
Results in clumping all the loads and adds fro mthe various iteration to introduce padding between the dependencies

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

### RISC 2.0 

