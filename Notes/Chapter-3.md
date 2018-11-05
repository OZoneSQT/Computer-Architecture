# Chapter 3 Summary

### Loop Unrolling
Results in clumping all the loads and adds fro mthe various iteration to introduce padding between the dependencies

###### Gap/Latency Table 
Certain types of instruction that follow each other impose a number of stalls when faced with a flow dependency

Loop Unrolling is static schedualing.
- does not handle dynamic runtime latency

Dynamic schedualing - out of order pipelineing
- no linear order
- follows graph branches
- changes when branch delays ( stalls )

### RISC 2.0 

