An application of [[07 Bipartite Graphs|bipartite graphs]] for effectively matching things together. 

Generally we have two sets of nodes, one example would be where we had one set for jobs $V_{2}$ and one set for people that can do those jobs $V_{1}$.

If each person can do some of the jobs, a matching gives an assignment of people to jobs. 
![[Pasted image 20231019151613.png | center | 200]]

In a matching you can only match one vertex from $V_{1}$ to one vertex from $V_{2}$

A maximal matching has the most people matched to jobs. IE, It contains as many edges as possible.

#### Worker-Assignment problem
This is just one of many example applications of maximal matchings. 
Given a set of workers and a set of tasks to be assigned. 
Constraints:
- Each worker is able to preform a subset of the tasks. 
- Each worker can do at most one task at a time. 
Goal:
- Assign as many workers as possible to as many of the tasks as possible


### Bipartite Matchings

Given a bipartite graph $G = (V_{1} \cup V_{2}, E)$ find a set of edges $S \subseteq V_{1} \times V_{2}$ that is a matching and is as large as possible.  
- $V_{1}$ and $V_{2}$ are given, hence we don’t have to find them.  
- $S$ is a perfect matching if every vertex is matched.  
- Maximum is not the same as maximal

#### Maximum vs Maximal
A maxing in a graph is a set of pairwise, non-adjacent edges.
- Each vertex can be associated with at most one edge of the matching
![[Pasted image 20231019153717.png | center | 600]]

- A maximal matching is a matching which is not a proper subset of any other matching
- A maximum matching is one with the largest possible number of edges (over all possible matchings)

#### Greedy Algorithm 
You can use a simple greedy algorithm for finding a *maximal matching*. 
- Iterates over all edges $e \in E(G)$
- Adding each edge to a maximal matching $M$ if it is non-adjacent to anything already in $M$

A maximal matching may have fewer edges than a more desirable maximum matching
![[Pasted image 20231019154231.png | center | 600]]

This algorithm is not good for finding maximum matchings

#### Maximum Matching Algorithm
Given a path $M$, an *alternating path* is a path in which the edges of the path alternate from being in the matching and not: e.g., Ann-Bob-Cher-Doug-Eve (left).

An *Augmenting path* however is an alternating path that starts from and ends on unmatched vertices: e.g., Eve-Doug-Cher-Fred (right).

A path can be both augmenting and alternating
![[Pasted image 20231019154417.png | center | 500]]

There is always one more non-matching edge than matching edge in an augmenting path:  
e.g., Eve-Doug-Cher-Fred (left)

If we find an augmenting path, remove from 𝑀 its matching edges (e.g., Doug-Cher), and  
add to 𝑀 its non-matching edges (e.g., Fred-Cher and Doug-Eve)

If there is no augmenting path, 𝑀 is a maximum matching
![[Pasted image 20231019154943.png | center | 500]]
##### Example
![[Pasted image 20231019155405.png]]
##### Observations

Two endpoints of an augmenting path must be on different sides of the bipartition.

Given an augmenting path $P$ with respect to a matching $M$, if we set $M^{\prime} = (M-P) \cup (P-M)$, then we obtain a matching $M^{\prime}$ of larger size.  
- The size of $M^{\prime}$ is exactly one unit larger than the size of $M$.  
- Hence, we can form a larger matching $M^{\prime}$ from $M$ by taking the edges of $P$ not in $M$ and (i.e., $P-M$)  
- Adding them to $M^{\prime}$ while removing from $M^{\prime}$ the edges in $M$ that are also in the path $P$ (i.e., $M-P$)  
- We say that we have augmented $M$ along $P$
![[Pasted image 20231019155929.png | center | 500]]

##### Finding an Augmenting Path
An augmenting path can be found using BFS
![[Pasted image 20231019160039.png | center |400]]
![[Pasted image 20231019160248.png | center  | 200]]

##### Overview
A polynomial-time algorithm, i.e., $\Theta(n^k)$ for $K\geq 0$ to find a maximum matching in a bipartite graph.

Augmenting a given matching 𝑀  
1. Start from an unmatched vertex 𝑥  
2. Build a tree (via BFS) of alternating paths away from 𝑥  
3. If another unmatched vertex 𝑣 is reached, an augmenting path is found  
4. If all vertices are visited, then no augmenting path exists starting at 𝑥  

![[Pasted image 20231019160636.png | center | 300]]
#### Hopcroft-Karp algorithm
![[Pasted image 20231019160706.png | center | 600]]

#### Time complexity
The running time of one call of `findAugmentingPath` is the same as the running time of BFS.

In each call, each vertex is added to the queue 𝑄 at most once  
- If we find an augmenting path then our best matching increases by one. 
- Since a maximum matching is bounded by $⌊𝑛/2⌋$, we only need to find at most $\Theta(n)$ augmenting paths.  
- Adjacency matrix representation: $\Theta (n^{2})$
- Adjacency list representation:  $\Theta(m)$


We potentially need to call `findAugmentingPath` for each unmatched vertex  
- This is bounded by Θ(𝑛), and repeat the process for each modified matching.  

The total running time to find a maximum matching is at most  
- Adjacency matrix representation: Θ(𝑛 " )  $\Theta (n^4)$
- Adjacency list representation: $\Theta(n^{2}m)$