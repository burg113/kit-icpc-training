20.04.2026 
Lucas Schwebler

 # MONOTONE STACK 

example problem:
array: 2 1 3 4 4 1
for each index i find next index to the left whith a smaller number max {j | a_j<a_i}
 - can be done with many DS in log time (segment tree, set etc)
 - linear time solution with monotone stack
idea: maintain a stack of elements, such that they are ordered in an increasing order, if we get a new smaller element we want to add, pop all larger elements from the stack
 
---
^2 1 3 4 4 1 

stack: [ ]

---
 2^1 3 4 4 1
   
stack: [(2,0)]

---
2 1^3 4 4 1 
    
stack: [(1,1)]

---
 2 1 3^4 4 1
       
stack: [(1,1),(3,2)]

---
 2 1 3 4^4 1
         
stack: [(1,1),(3,2),(4,3)]

---
2 1 3 4 4^1
           
stack: [(1,1),(3,2),(4,4)]

---
2 1 3 4 4 1^
            
stack: [(1,5)] 

---


always find previous element at top of stack
[
 note that in this specific problem, we do not need to explicitly maintain the stack, we can instead use the answer array as pointers to previous stack elements
]

this problem occurs many times in problems. For example:

**largest area under histogram**

    given an array a, find indices i<j and a height h such that h>=a[k] forall i <= k <= j and (j-i+1)*h is maximal


