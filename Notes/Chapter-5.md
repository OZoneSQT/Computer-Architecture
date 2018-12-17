# Chapter Five Summary

##### Synchronization
Easy and great solution!
- Gap between latches
- locks and atomics
   - bad performance
   - low programability
- load block store condition

> My notes really sucked...

Fetch execute cycle: delay progression waiting for an operation

only time an _Invariant_ is false is when some peice is within the atom

###### Memory Consistency
- How do you sync a program?
   - "Parrallel"
  
- write back
    - tell mempry the best you know about value
- flush
   - throw your value into memeory and walk away
   
