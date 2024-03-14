#### Muddy City Problem
A city is muddy after a storm and to fix this you need to pave paths.

1. Enough streets must be paved so that it is possible for everyone to travel from their house to anyone else’s house only along paved roads, and
2. The paving should cost as little as possible.

![[Pasted image 20231029122544.png | center | 500]]
### Spanning tree
Definition: A spanning tree of a graph $G$ is a connected acyclic graph $T$ such that it contains all the vertices of $G$ eg $V(T) = G(T)$ ,and a sub set of edges of $G$

On a spanning tree $T$ of $G$, we can walk from any vertex of $G$ to any other vertex of $G$ along exactly one path whose edges are part of the tree $T$

Here $G_{1}$ is a spanning tree, however $G_{2}$ and $G_{3}$ are not
![[Pasted image 20231029123043.png | center | 300]]

### Minimum spanning tree 
Minimum spanning tree (MST) problem on a weighted connected graph 𝐺:

Find a spanning tree (subgraph containing all vertices that is a tree) of minimum total weight.

Minimum spanning trees are not unique (There can be more than one of them)

There are two efficient *greedy* algorithms for finding MSTs, Prim’s and Kruskal’s.  
• Each selects edges in order of increasing weight but avoid creating a cycle.

>[!info]- Definition: Greedy Algorithm
>A greedy algorithm is an algorithm which makes the optimal choice at each small stage with the goal of this eventually leading to a globally optimum solution

**Prim’s**
- Prim’s algorithm maintains a tree at each stage that grows to span  
- Prim’s implementation is very similar to Dijkstra, it runs in $O((m+n)\log n)$ time

**Kruskal’s**
- Kruskal maintains a forest whose trees are combined in to one spanning tree
- Kruskal can be implemented to run in time $O(m\log n)$

Demonstrations of algorithms: https://auckland.au.panopto.com/Panopto/Pages/Viewer.aspx?id=f13d67e7-d371-445a-b446-b09600e2d78a
#### Finding spanning trees
Finding a spanning tree is easy.  
1. Run DFS or BFS from any of the vertices.  
2. If $G$ is connected, there will be only one search tree and this will be a spanning tree.

### Prims Algorithm
Prim's algorithm is a modified version of Dijkstra's. 
- We pick one of the vertices in $G$ as the source. 
- We need to keep track of the edges used to reach a vertex.
![[Pasted image 20231029141736.png | center| 500 ]]
`q.decreaseKey(x, t)` changes the **value** associated with x only if `t` is less than its current value. 

#### Time Complexity
The algorithm is essentially the same as Dijkstra's so therefore the time complexity is the same

$$
O((m+n)\log n)
$$

#### Correctness
![[Pasted image 20231029144039.png|center|500]]
![[Pasted image 20231029144212.png | center |  500]]
### Kruskals Algorithm
Prim’s builds an MST using black vertices while Kruskal’s builds a forest that then converges to an MST. 

Both algorithms build the tree avoiding to add cycles  

1. First sort the edges by their weights  
2. Add an edge to the forest if the edge does not connect two vertices already in the same tree  
![[Pasted image 20231029144751.png | center | 600]]

Every time you find an edge between two different trees (sets of nodes), you add the edge then merge the two sets into one with a union operation. 

#### Example
![[Pasted image 20231029155405.png | center | 600]]

Cost of a MST is the sum of all the weights

#### Disjoint set Data structure
This ADT is used to represent a collection of sets containing objects that are related to each other. also called [[6. Union Find |Union find]]. Allows us to do these operations in constant time. 

There are two standard operations:
- **Union** - merges two sets by there names (denoted by one of the objects in a set)
- **Find** - Given an object, return the set that it belongs to (its parents)

This data structure allows us to navigate the tree

#### Time complexity
The time complexity is dominated by having to sort the edges by weight $O(m\log m)$
- $m$ is at most $n^2$ and $\log_{2}(n^2) = 2\log_{2}(n)$
- therefore $O(m \log n)$


### MST Summary
The most expensive edge, if unique, of a cycle in an edge-weighted graph 𝐺 is not in  
any MST. (Otherwise, at least one of those equally expensive edges of the cycle  
must not be in each MST.)  

The minimum cost edge, if unique, between any non-empty strict subset 𝑆 of  
$V(G)$ and the $V(G)\setminus\S$ is in the MST. (Otherwise, at least one of these minimum cost  
edges is in each MST.)

### Comparison between algorithms
![[Pasted image 20231029160641.png | center | 600]]