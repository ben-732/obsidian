![[Pasted image 20240509195252.png]]

![[Pasted image 20240509195403.png]]
This is all done behind the scenes, when you write a query the DBMS finds the most optimal. 

Evaluation plan: Estimate the cost of an operator, which depends on.

- Metadata: The size of the underlying relation 
- Implementation: table scan? Using indexes (B+tree/hashing/…) 
- Reporting: whether the result needs to be stored on disk 

(In this course, we assume that we don’t need to store the query results on the disk)


### Overview
Basic Steps in Query Processing 
1. Parsing and translation
	- SQL to relational algebra expression 
	- Parser checks syntax and verifies relations
2. Optimization
	- Explore equivalent relational algebra expressions
	- Minimize the estimated cost (especially # of I/Os)
	- Generate an execution plan.
3. Evaluation
	- The query-execution engine takes a query-evaluation plan
	- Executes that plan
	- Returns the answers to the query.

![[Pasted image 20240509195556.png | center | 300]]

### Space Arrangement
The External Memory Model 

Denote by $B$ the block size of the system. Let $M$ be the size of the main memory.

The cost of an algorithm is decided by the number of blocks (# of I/Os) transferred between the main memory and the disk. 

Sometimes we use $m=M/B$ pages as the size of the main memory to simplify the analysis. In this scenario, the allocation of the memory is in the pages.

Arrangement of the memory:
- $m-x-1$: Hold one relation 
- $x$: buffer pages for the other relation 
- 1: buffer for the output
### Physical Query Plan Operators
#### One Pass algorithms
Tuple-at-a-time, unary operations: selection and projection
![[Pasted image 20240509195813.png | center | 300]]
#### Iterators 
- Combines several operations into one 
- Avoids writing temporary files 
- Many iterators may be active at one time

![[Pasted image 20240509195919.png | center | 400]]
#### Multi Pass algorithms
##### Multi-way Merge Sort
Sorting a relation, the following relational operators can be then facilitated with a one-pass scan.
- Duplication elimination 
- Grouping and aggregation 
- Union 
- Intersection and difference

- Merge join 
- Hash join

##### Block nested-loop join
Represented by $R \bowtie S$

Block nested-loop join leverages the buffer pool to optimize the # of I/Os of the join $r\bowtie s$ without any condition.

![[Pasted image 20240509201007.png | center | 300]]

**Join attributes:** the common attributes of $r$ and $s$
**Outer and inner relations:** Use a nested loop to facilitate the join, the relation scanned in the outer loop is called the outer relation, and the one in the inner loop is called the inner relation.

Assume $r$ is stored in $n_{r}$ blocks and $s$ is in $n_{s}$ blocks

For each chunk of $m-x-1$ blocks of $r$
- Read blocks into main memory
- Organise the tuples into search structure with search key as common attributes of $r$ and $s$
- For each block $b_{s}$ of $s$ (Assuming x is 1??)
	- Load $b_{s}$ into memory
	- For each tuple $t$ of $b_{s}$
		- Find the tuples of $r$ in the buffer that join with $t$
		- Report the result

Here we have $\Large \lceil \frac{n_{R}}{m-x-1} \rceil$ iterations Which will cause a IO count of $\Large \lceil \frac{n_{R}}{m-x-1} \rceil \times n_{s} + n_{r}$ IOs

Block nested loop is useful as it can handle unsorted relations, it has no conditions it needs to be met. 

The worst case will be if we can only load 1 block from $r$ at a time, this will cause $\Large n_{r}\times n_{s}+n_{r}$ IOs.

The best case (When we have infinite memory) then we can complete in $\Large n_{s} + n_{r}$ IOs

We prefer to put the smaller of the two relations as the outer relation as maths. The multiplication section is identical. 

##### Merge Join
Represented by $R \bowtie S$

Merge join either sorts the two relations on the join attributes, or saves the sorting cost if the two relations are already sorted on the join attributes. 

On two sorted relations, merge join conducts a synchronised scan, facilitating a sequence of “subjoin”s on the tuples that agree on the joined attributes. 
- `𝑟[𝑎1=𝑎]⋈𝑠[𝑎1=𝑎]` 
- `𝑟[𝑎1=𝑏]⋈𝑠[𝑎1=𝑏]`

If $R \bowtie S$ and stored in $n_{r}, n_{s}$ blocks:
- Join attribute A
- Assume that $r$ and $s$ are both ordered on $A$
- For each value $x$ of $A$ shared by $S$ and $R$ (Synchronised scan)
	- Block nested loop join on sub relations (All relations sharing this value for A)

**Complexity:**
- Best case: $\Large n_{r} + n_{s}$ when $A$ is a key of $R$
- Worst case: Block nested loop join, all attributes have the same value. 
- Average Case: all join attributes have same frequency

![[Pasted image 20240516085930.png]]

##### Hash Join
Works by applying the same hashing function to both relations. 

1. Partition $r$ into $k$ buckets using hash function $h_{A}$
2. Partition $s$ to $k$ buckets using hash function $h_{A}$\
3. For each integer $i \in [0, k)$
    • Join on $r_{i}$ and $s_{i}$ (one pass/multi pass)

![[Pasted image 20240516090113.png | center | 300]]
![[Pasted image 20240516090545.png | Center | 600]]

#### Index Based Algorithms
Application of Index in 
- Selection operation 
- Join operation

To avoid/reduce the number of table scans. Statistics that could help estimate the cost of index- based selection and join:
- $v(A, R)$: The number of distinct values of attribute $A$ in relation $R$
- $|R|$ the number of tuples in relation $R$
- $n_{R}$ the # of blocks used to store relation $R$.  
- Histogram,etc.

![[Pasted image 20240516091701.png | center | 600]]
![[Pasted image 20240516093847.png | center | 400]]
![[Pasted image 20240516094228.png | center | 550]]


##### Index Nested Loop Join
Load one tuple at a time ($t$,) Get all tuples of $S$ that can join with $t$ using the index. 
- Unordered $O(k)$ I/Os k = # of tuples
- Ordered: $O(b)$ I/Os  b = data blocks

This means that the cost is dependent on whether $s$ has been sorted on $A$ (sequential). For each $t$:
- Sequential, can use index to find first block of `t[A]` in $s$ then read sequentially. $\Large \frac{n_{s} }{V(A, s)}$ I/Os
- Non sequential: Get and access $\Large\frac{|s|}{V(A, s)}$ index pointers to get the tuples in s. 

![[Pasted image 20240516183415.png| center | 550]]

![[Pasted image 20240516221405.png | center | 550]]

Here the total IOs is $50000 + 1000$. 

There is no clear answer to weather index nested join or block nested loop join is better, it is to-do with the space arrangement. In this cause, the best case for block nested is much better, $1000+500$ while the worst case is $500\times 1000 + 500$

The amount of memory available is a deciding factor...

##### Complex Selection operations
![[Pasted image 20240516221923.png  | center | 600]]
Databases typically implement A9 as they are more effective. 

### Evaluation of Expressions
Alternatives for evaluating an entire expression tree:
- **Materialisation:** generate results of an expression whose inputs are relations or are already computed, materialize (store) it on disk. Repeat. 
- **Pipelining:** pass on tuples to parent operations even as an operation is being executed

#### Materialisation
Evaluate one operation at a time, starting at the lowest-level. Use intermediate results materialized into temporary relations to evaluate next-level operations.

#### Pipelining
Evaluate several operations simultaneously, passing the results of one operation on to the next without creating temporary relations. 

Much cheaper than materialization: no need to store a temporary relation to disk. 

>Pipelining may not always be possible (e.g., sort, hash-join)


## Query Optimisation
Used to determine what operators we run and in what order. 

Alternative but equivalent expressions of evaluating a given query
![[Pasted image 20240516231009.png | center | 600]]

An evaluation plan defines exactly  
- What algorithm is used for each operation
- How the execution of the operations is coordinated


### Cost Evaluation Overview
Steps in cost-based query optimization
1. Generate logically equivalent expressions using equivalence rules
2. Annotate resultant expressions to get alternative query plans
3. Choose the cheapest plan based on estimated cost
    

Estimation of plan cost based on:
- Statistical information about relations. 
	- number of tuples, number of distinct values for an attribute
- Estimation for intermediate results to compute cost of complex expressions
- Cost formulae for algorithms, computed using statistics

#### Evaluation Plans
Most database support `explain <query>`
- Displays plan chosen by query optimiser, along with cost estimates 
- Some syntax variations between databases

PostgreSQL shows actual runtime statistics found by running the query, in addition to showing the plan

### Equivalent Rules
if $E_{1}$ and $E_{2}$ are two expressions, then they are equivalent if they generate the same set of tuples on every legal database instance $D$. 

There are 12 Common rules (used in this course) that define equivalent expressions. 

![[Pasted image 20240517112626.png | Center | 500]]

![[Pasted image 20240517112729.png | Center | 500]]
![[Pasted image 20240517112821.png  | center | 500]]

![[Pasted image 20240517113037.png | center | 500]]
![[Pasted image 20240517113336.png | Center | 500]]

![[Pasted image 20240517113619.png  | center | 500]]
### Equivalent Expression generation
![[Pasted image 20240517114524.png | center | 600]]

Recursively applies the 12 rules to E then all results of E until no more can be applied, finds the most optimal expression. This is known as exhaustive search and is expensive. 

### Join ordering
If $r_{1}\bowtie r_{2}\bowtie r_{3}$ them commutativity applies. If $r_{1} \bowtie r_{2}$ is small and $r_{2} \bowtie r_{3}$ is large, we generally will choose $(r_{1} \bowtie r_{2}) \bowtie r_{3}$. So we end up with a smaller temp relation. 
![[Pasted image 20240517115141.png | Center | 400]]

## Cost Estimation
Use statistics to estimate the cost of each expression
- Statistical information about relations: Number of tuples, # of distinct values for an attribute
- Statistical estimation for intermediate results  
- Cost computed for algorithms of join, selection, etc..

Notation:
- $|r|$ - The number of tuples in relation $r$
- $n_{r}$ -the # of blocks that contain tuples of $r$
- $l_{r}$ - the size of a tuple of $r$
- $f_{r}$ - the blocking factor of $r$ (number of tuples of $r$ in a block)
- $V(A,r)$ the number of distinct values of $A\in r$

If tuples of 𝑟 are stored in a file, then $\Large n_{r} =O\left( \frac{|r|}{f_{r}} \right)$

The input of an operation can be the result of a sub-expression, therefore, we need to estimate the size of expression results. This can be refined if histograms are available. 

![[Pasted image 20240517120421.png | center | 500]]

![[Pasted image 20240517204735.png | Center | 500]]
![[Pasted image 20240517204750.png | center | 500]]
![[Pasted image 20240517205109.png | center | 500]]
![[Pasted image 20240517205455.png | center | 500]]
![[Pasted image 20240518093943.png | center | 500]]

## Cost Based Optimisation
Practical query optimizers incorporate elements of the following approaches. 
- Exhaustive search: search all the plans and choose the best plan in a cost-based fashion. 
- Heuristics: use heuristics to choose a plan. 
- AI for DB: a newly emerged direction currently is research active.
### Exhaustive Search
When finding the best join order for $r_{1}\bowtie r_{2} \bowtie\dots\bowtie r_{n}$

If $n=3$ then there are 12 different join orderings. If it is 7 then there are 6553280 and when $n=10$ then the number grows to over 176 billion. This is factorial level big o. 

Instead we use dynamic programming, the least cost join order for any subset of $\{ r_{1}, r_{2}, \dots, r_{n} \}$ can bring down the complexity to $O(3^n)$. Space complexity is $O(2^n)$

### Heuristics
To reduce the size of the intermediate results, we: 
- Perform selection early -> reduces the number of tuples 
- Perform projection early -> reduces the number of attributes 
- Perform most restrictive selection and join operations (i.e., with smallest result size) before other similar operations.

### Pairwise Join Plan
Left deep join plan: the right-hand-side input for each join is a relation, not the result of an intermediate join.
![[Pasted image 20240518094847.png | center | 400]]

Many optimizers consider only “left-deep join” 
- Optionally combine with heuristics to push selections and projections down the tree. 

Some repeatedly pick the “best” relation to join next (start with small relations). 

To find the best execution plan of a relational algebra expression $E$
- Focus on the set of equivalent expressions of $E$. 
- Estimate the cost of a plan with statistical information on each relation/attribute. 
- Optimize the search for the best execution plan.