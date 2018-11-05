# Appendix C Summary
- Memory Wall is the _greatest challenge_ for mordern day computer architecture. Applies to latency in general.
- How to calculate the runtime of a program
```
Seconds     instruction     cycles          seconds
-------- = ------------- x ------------- x ---------
program     program         instruction     cycles
```

### Fundamentals
- **Amdohl's Law**:  T<sub>_t_</sub>(x) = ( T<sub>_s_</sub> / ( T<sub>_p_</sub> / x ) ); where _x_ is the degree of parallelism

### RISC 1.0
Fetch (f) | Decode (d) | Execute (x) | Read (m) | Write (w)
----------|------------|-------------|----------|-----------

- Fetch:
   - Read `ProgramCounter`
   - Gets the next instruction from the `I-Cache` ( instruction cache )
- Decode: 
  - Interprete instruction
  - get values from CPU memory
  - Branch prediction >> Added later...
- Execute:
  - Calculate memory address ( load / store )
  - Use correct ALU to perform instruction
  - branch instruction ???
- Read: 
   - Copy value from register file `D-Cache` to CPU memory
   - _Done during __second__ half of clock tick_
- Write:
   - Set value from CPU memory to register file
   - _Done during __first__ half of clock tick_

> Inbetween each box is a latch name _left-hand side / right-hand side_. A latch contains the output of the left side and is equivelant to the input of the right side. Any box can access and use the values inside any latch ( not strictly it's own left and/or right latches ). This is called forwarding.

```
Bandwidth = 1 // One new instruction per cycle can be feed into pipe in program-order
Depth = 5     // Number of stages to process an instruction ( degree of paralellism )
Full pipeline === Equilibrium // One instruction is completed per cycle

Effective Speedup = # of boxes !
```

__Limitation__: 1 box and only perform one operation. box will wait on an instruction data to be available before continuing. These are called _Stalls_.

### Hazards
1. Structural Hazard
   - Hardware unavailable: `float` multiple has to wait while that ALU is in use
2. Data Hazard
   - Instruction requests unavailable data: Output of previous instruction has yet to be produced. Data/Flow dependency
3. Control Hazard
   - Branch decision unavailable

Hazards produce stalls with affect the Speed-Up seen by a program when pipelining. `S = pipeDepth / 1 - ( stallCounter / instructionCounter )`.

### Dependencies
- Flow
   - Structural, Data, Controll Hazards
- Output
   - ???
- Anti
   - Flipping order of instructions would cause program inconsistences.

![image](https://user-images.githubusercontent.com/16867443/47973914-e190ee00-e074-11e8-8bc7-9ab8e6c8556e.png)

### Multi-Cycle Instructions
Instruction can take a variable amount of time at each box. Class convention:
- mul.d takes four X cycles

### Loop Timing
Loop cycle = completeion of D-box for bne instruction.

loop cycle * number of iteration - 1 + the maximum duration of the loop. 
