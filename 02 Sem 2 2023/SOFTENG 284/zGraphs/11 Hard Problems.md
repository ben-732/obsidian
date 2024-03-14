These are problems without polynomial time solutions

### P vs NP problem
This is a major unsolved problem in theoretical computer science

  
Basically, asking if every problem whose solution can be quickly verified can also be quickly solved, i.e., P=NP?

  
Polynomial time: the running time is upper bounded by a polynomial expression in the size of the input for the algorithm, i.e., $T(n) = O(n^k)$ for some constant $k\geq 0$.

![[Pasted image 20231029161108.png | center | 300 ]]

### Hard problems
There are lots of graph/networking problems

- Many do not have easy or polynomial-time solutions, i.e., *quickly solvable*. Best approach: Try all possible solutions.  
- However, a few of them are in a special category in that their solutions can be verified in  polynomial time (NP), i.e., *quickly checkable*  
- Many of these are proven to be harder than anything else in NP (*NP-hard*).  
- Other algorithm design techniques like backtracking, branch-and-bound or approximation  algorithms are needed. (COMPSCI 320, 720)  

There are two main views of the types of problems
![[Pasted image 20231029162601.png]]

#### Common problems
- Hamiltonian Cycle Problem: Is there a cycle that uses all vertices? (decision problem)  
- Traveling Salesman Problem: Is there a cycle of length $|G|$ in an edge-weighted graph $G$ with cost at most $c$?  
- 𝒌-colouring Problem: Is there a 𝑘-colouring of a graph, for fixed 𝑘 ≥ 3?  
- Independent Set Problem: Is there a subset of 𝑘 vertices such that no two are adjacent?  (optimisation problem: find a maximum-size subset)  
- Vertex Cover Problem: Is there a subset of 𝑘 vertices such that every edge is covered?  (optimisation problem: find a minimum-size subset)  
- Dominating Set Problem: Is there a subset 𝐷 of 𝑘 vertices such that each vertex is in the  neighbourhood (distance ≤ 1) of 𝐷

##### Hamiltonian Cycle
Given a graph $G = (V, E)$, does it contain a [[Graph Terminology#Hamilton Paths / Cycle|Hamiltonian cycle]]?  
1. A wooden dodecahedron (with 12 regular pentagons as faces), with a peg at each vertex, labeled with the names of different cities. String was used to plot a cycle visiting 20 cities exactly once  
2. The graph form of the puzzle  
3. The solution (a Hamilton circuit)

##### Traveling Salesman problem (TSP)
- Travelling Salesman Problem: Given a graph $G = (V, E)$, is there a cycle of length $|G|$ in an edge-weighted graph 𝐺 with a cost at most $c$?  
- Essentially, TSP searches for the shortest route a travelling salesperson should take to visit a set of cities.

This problem reduces to finding a Hamilton circuit such that the total sum of the weights of its edges is as small as possible.

##### K-Colouring Problem  
Given a graph $G = (V, E)$ it there a 𝑘-coloring of the graph such that no two adjacent vertices are of the same color $k\geq 3$

##### Vertex cover problem
Given a graph $G = (V, E)$, is there a subset of $k$ vertices such that every edge is covered?  
• Input: Graph instance  
• Output: Smallest number $k$ such that $G$ has a vertex cover of size $k$

The endpoints of any maximal matching form a vertex cover.  
The complete bipartite graph $k_{m,n}$ has a minimum vertex cover of size $\text{min}(m,n)$  

##### Independent set
Given a graph $G = (V, E)$, and $k\in \mathbb{N}$, does $G$ contain an independent set of size $k$ such that no two are adjacent?  
• Input size $n=|V|$
• Certificate: $k$ vertices  
• Verifier: Check that there are no edges between pairs of vertices


### Remember?
$p \subset np$ so it is $np$ if it is $p$