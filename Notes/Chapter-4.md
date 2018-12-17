# Chapter Four

#### Topics
- GPUs
- Vector Processors

### Vectors
> Data Parallel Operations only to make class trivial...

Vector Chaining ==> Latency tolerency champions
- Perfect for data parallel computation
   - weather forecasting

- CPUs : One operand/instruction at a time
- Vectors : 64 operands/instruction at a time 
> 64 is an arbrary example

```cpp
v1 = { 1,3,4,6,3,7, /* ... */ 9, 5,1};
v2 = { 6,7,8,6,2,7, /* ... */ 8, 4,6};
v1 = v1 * v2; // All ops done at once!
```

- Internal memory architecture is pipelined ! very efficient !

**Limitations**
- High branch frequency
- long scalar dependencies
> same problems plague RISC CPUs

##### Little's  Law 
`C = Bandwidth * Latency` C ~ capacity === tolerence

In terms of resource efficency...
Scaling RISC CPUs x<sup>10</sup> growth in complexity whereas vectors growth would be linear...
This is due to vectors dependency checking // Keep reading we get there...
```
lv v1, ra === l.d f6, O(r2)
```
x128 times more work per instruction in the example ...
instruction travel is our primary cost ...
fetch bandwidth + decode bandwidth come at 1/128 the cost compared to scalar ! huge savings ...
those 128 opts were independent, thus no dependency checking was required == saving throught reduced overhead with shift in granularity

more advantages:
- hw highly optimized for loads
- replace loops ( in scalar ) to vector instructions --> eliminats branch instructions ( primary killer of performance on scalar )
- more power efficent !
- far fewer stalls compared to RISC/1.0
- no cache missing since no cache !

```cpp
// SaxPy example..
template<typename type>
void saxpy(type a, std::vector<type> x, std::vector<type>& y) {
   y = ( a * x ) + y;
   // load a
   // load x
   // mulvs.type a * x = v1
   // load y
   // addv.type v1 + y = v2
   // sv v2 -> y
}
```

##### Execution Time
- instruction

vector lanes added to "parallelize" execution of instruction, moves less data shorter distances thusly saving more energy!
_t_(vector instruction) = vector register length / # of lanes

- program

Two methodologies
- stupidly simple: dont put two flow dependencies in the same group
- realalistic: dont exceed functional units

> still must respect program order

Example
```
L1 --> M2 -----> A4
          L3 --> A4 --> S5
```

- Option A: Simplified
```
 [ L1 ]   [ M2, L3 ]    [ A4 ]   [ S5 ]
12 + N | max(7,12) + N | 6 + N | 12 + N == 42 + 4N
```

- Option B: Realistic
> assuming two load units, start loads at teh same time for best result !
```
[ L1, M2, L3, A4, S5 ]
12 + 7 + N/A + 6 + 12 + N == 37 + N
```

```
N = vec reg len / # of lanes
```

##### Vector Chaining
- Sequential
```
|-------|---------------------|------|---------------------|
    7            n                6              n           == 13 + 2N 
```

- Chaining
```
|-------|---------------------|           // Start as soon as first retval is avaiable !
    7   |------|---------------------|
           6              n            == 13 + N
```

##### Memory Access Patterns
Specialized by Harware
- Unit stride: each index of an array
- constant stride: every i-th element
- scatter gather: random list

> GPU's have tiny memory which means the data must be preloaded || hardcoded in advance !
