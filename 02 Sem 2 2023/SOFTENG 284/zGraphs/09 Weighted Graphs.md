- Very common in applications, also called “networks". Optimisation problems on networks are important in operations research.  
- Each arc carries a real number “weight", usually positive, can be $+ \infty$. Weight typically  represents cost, distance, time. We may use the words “weight” and “cost” interchangeably here.  
- Representation: weighted adjacency matrix or double adjacency list.  
- Standard problems concern finding a minimum or maximum weight path between given nodes (covered here), spanning tree, cycle or tour (e.g., TSP), matching, flow, etc.  

>[!info]- Example: Weighted Graph
>![[Pasted image 20231004132023.png | center | 500]]

### Representing Weighted Graphs

Weighted graphs can be represented as both adjacency matrices and double adjacency lists
![[Pasted image 20231004132054.png | center | 300]]

![[Pasted image 20231004132159.png | center | 300]]
### Walks and Paths in weighted Graphs
For a digraph (𝑉, 𝐸) with arc weights {𝑐(𝑢, 𝑣) | (𝑢, 𝑣) ∈ 𝐸} we say that the distance 𝑑(𝑢, 𝑣) between two vertices 𝑢 and 𝑣 of 𝑉 is the minimum cost of a path between 𝑢 and 𝑣. The cost of a walk/path $v_{1}, v_{2}, \dots, v_{k}$is $\sum^{k-1}_{i=0}c(v_{i, }v_{{i+1}})$

The diameter of a (di-)graph $G = (V, E)$ is the maximum of 𝑑(𝑢, 𝑣) over  all pairs $u, v \in V$. If the (di-)graph is not [[Path Theorems#Connectedness in Directed Graphs|strongly connected]], the diameter of 𝐺 is not defined.  

### Negative weights
- The vast majority of weighted graph problems have positive weights  
- However, exceptions exist where the weight of an edge/arc can be negative.

- Consider going on a holiday trip around the world.  
- Between some cities, we may need to buy a train/ferry ticket or an airfare (cost, positive weight),  but between others we might have the opportunity to earn money by working as  crew on a ship, train, or plane (=gain, negative weight).  

### Eccentricity
The eccentricity of a vertex 𝑣 in $V(G)$, denoted as e(𝑣), is the **maximum** of the distances between 𝑣 and any other vertex 𝑢 in 𝑉(𝐺).

![[Pasted image 20231004134106.png | center | 500]]
Distance here is defined on two vertices while eccentricity is defined on one. 


###  Shortest path algorithms (SSSP/ASSP)
Given an originating node $v$, find the shortest (minimum weight) path to each other node. 

If all weights are equal then BFS works, otherwise not. 


#### Algoritms
- **Dijkstra** (pronounced "Dyke-stra"): Used to find the cost to each destination vertex  
from a single source ("single source shortest path" - SSSP). Cannot handle negative  
weights
. 
- **Bellman-Ford**: solves SSSP as well, slower than Dijkstra but can handle negative  
weights 

- **Floyd-Warshall**: solves all-pairs shortest path (APSP) problem – minimal cost between  
any given pair of vertices


#### Dijkstra
Dijkstra's algorithm is an example of a greedy algorithm as at each step we only consider what is the best option at the next step and not the overall optimal step. 

1. Maintain a list of S visited nodes
2. Choose an unvisited node $u$ with the shortest $S$-path and put it into $S$
3. Update distances of remaining unvisited nodes from starting node $s$ in case adding $u$ has established shorter paths
4. Repeat. 

>[!info] 
>Let an $S$-Path be a path starting at node $s$ and ending at node $u$ with all possible nodes in $S$ except possibly $u$

^4e8294

![[Pasted image 20231005131830.png | center | 500]]

##### Time Complexity 
Complexity with array implementation to find the minimum value of 𝑑𝑖𝑠𝑡: $\Theta(n²)$ 
- $n^2 + m$ Adjacency list representation  
- $n^2 + n^2$ Adjacency matrix representation  
- $n$: time taken to find the node with minimum value of $dist$

Complexity depends on the data structure used. For priority queue, $\Theta((m+n)\log n)$ is possible:
- $n$ delete-min operations
- $m$ decrease-key operations
- $\log n$ binary heap implementation of priority queue. 

##### Why the algorithm works
**Fact:** Suppose that all weights are non-negative. At the top of *while* loop, these properties hold for all $x \in V(G)$:
- P1: `𝑑𝑖𝑠𝑡[𝑥]` is the minimum weight of an S-path to `𝑥`.  
- P2: if `𝑐𝑜𝑙𝑜𝑟[𝑥]=BLACK`, `𝑑𝑖𝑠𝑡[𝑥]` is the minimum weight

In other words:

- P1 guarantees that, in each iteration of the algorithm, we find a minimum weight path from 𝑠 to a vertex 𝑤 ∈ 𝑉(𝐺) $w \in V(G)$ that only uses vertices in $S$ (black vertices) except possibly $w$.

- P2 guarantees that, for each black vertex $w$, we have already found a minimum weight path from $s$ to $w$.  

Taken together, these two facts imply that Dijkstra’s algorithm solves the single-source  shortest path problem for weighted digraphs that do not have any negative arc weights.

##### Limitations
One of the properties of Dijkstra’s algorithm is that once a node turns black its distance is not updated any more.   

This means that even if a new cheaper route is available, a black node’s distance will not get updated – a case that would only be there when there are *negative weights*.  


#### Bellman-Ford
Bellman-Ford can solve SSSP as well, it is slower than Dijkstra but can handle negative weights.

Bellman-ford performs at most $n$ iterations, where $n$ is the number of nodes/vertices  

![[Pasted image 20231028110213.png | center | 500]]

There will be at most $n-1$ iterations. 

Slower than Dijkstra’s algorithm when all arcs are nonnegative.  

Similar idea as in Dijkstra’s: to find the single-source shortest paths by progressively relaxing restrictions.  

- Dijkstra’s: one node a time based on their current distance estimate.  
- Bellman-Ford: all nodes at “level” $0, 1, \dots, n-1$ in turn.  
	- Level of a node $v-$ the minimum possible number of arcs in a minimum weight path to that node from the source $s$.  


**Theorem:** If a graph $G$ contains no negative weight cycles, then after the $i$-th iteration  
of the outer for-loop, the element `dist[𝑣]` contains the minimum weight of a path to  
$v$ for all nodes $v$ with level at most 𝑖.  

This algorithm is (non-greedy) and handles negative weight arcs but not negative weight cycles. 

Numerical order example
![[Pasted image 20231028114436.png | center | 500]]
##### Running time
Runs in time $O(nm)$ since the two inner-most for loops can be replaced with: $for(x,v)\in E(V)$.

If the graph is dense, then $m$ will increase towards $n^2$ making the complexity $O(n^3)$

##### Why it works
Effectively, the algorithms ensures that `dist[v]` will never increase
![[Pasted image 20231028111213.png]]

##### Negative weight cycles
The base algorithm can be modified to detect negative weight cycle.

The SSSP problem makes no sense if we allow digraphs with cycles of negative total weight. 
![[Pasted image 20231028112638.png | center  | 400]]

You can detect a negative cycle by running the outer for loop for one more iteration. If `dist[v]` changes for some vertex $v$ in the last iteration, then the graph has negative weight. 

![[Pasted image 20231028113914.png | center  | 400]]
#### Floyd Warshall
Calculates shortest path between all pairs of nodes and can handle negative costs

Options for APSP
- Dijkstra provides the shortest path from one node to any other nodes in a graph  
	-   Run Dijkstra's algorithm starting at each of the $n$ vertices: $\Theta(n)$ for iterating through the vertices, and $O(n^2)$ for each Dijkstra run. Total: $O(n³)$
- Bellman-Ford is similar to Dijkstra but can handle negative costs  
	- Use the Bellman-Ford algorithm $n$ times: $O(n^2m)$ at best, $O(n^4)$ at worst
- Floyd-Warshall gives shortest paths between all pairs of nodes and can handle negative costs

##### Approach
Number nodes (say from 0 to $n-1$) and at each step $k$, maintain matrix of shortest  
distances from node $i$ to node $j$ not passing through nodes higher than $k$. Update at each  
step to see whether node $k$ shortens current best distance.  

Use triple nested for loops, so runs in $O(n³)$ times. Better than Bellman Ford $O(n^2m)$ for  
dense graphs.  

![[Pasted image 20231028115800.png | center | 500]]
![[Pasted image 20231028115838.png | center | 500]]
![[Pasted image 20231028115950.png | center | 500]]

This algorithm is based on the dynamic programming principles.  

At the bottom of the outer for loop, for each  $u,v \in V(G)$ `d[𝑢, 𝑣]` is the length of the  
shortest path from $u$ to $v$ passing through intermediate nodes that have been seen in  
for $x$ loop.
![[Pasted image 20231028120329.png | center | 250]]

**Theorem** at the bottom of the outer for-loop, for all nodes 𝑢 and 𝑣:

- `d[𝑢,𝑣]` contains the minimum length of all paths from 𝑢 to 𝑣 that are restricted to  using only intermediate nodes that have been seen in the outer for-loop.  
- When algorithm terminates, all nodes have been seen and `d[𝑢,𝑣]` is the length of the  shortest 𝑢-to-𝑣 path.

##### Why it works
**Theorem:** at the bottom of the outer for-loop, for all nodes $u$ and $v$: 

- `d[u, v]` Contains the minimum length of all paths from 𝑢 to 𝑣 that are restricted to using only intermediate nodes that have been seen in the outer for-loop.  
- When algorithm terminates, all nodes have been seen and `d[u, v]` is the length of the  shortest u to v path.  

![[Pasted image 20231029113649.png | center | 600]]
![[Pasted image 20231029114010.png | center | 600]]

##### Recording Predecessors
Keeping track of the shortest paths as well as their lengths

![[Pasted image 20231029114256.png | center | 450]]
###### Reconstructing path from predecessors
look along a row, at the immediate parents coming form row index (source) row 1 in the following image
![[Pasted image 20231029120628.png | center | 500]]
##### Computing actual shortest path
In addition to knowing the actual shortest distances, the shortest paths often need to also be reconstructed
![[Pasted image 20231029120950.png | center | 600]]

The shortest path between $i$ and $j$ can be output from the predecessor matrix $\Pi$ using the following recursive algorithm

![[Pasted image 20231029121230.png | center | 400]]

##### Cool things
- Essentially just three nested loops: $\Theta(n^3)$.  
- The outer loop specifies the potential "via" vertex, the inner two loops the end points of the path.  
- Generally faster than a Bellman-Ford algorithm repeated $n$ times.  
- Not necessarily faster than Dijkstra but handles negative edge weights correctly.  


#### Comparison
![[Pasted image 20231029121839.png | center]]

By detect it means return true or false if a negative weight cycle is present. None of these algorithms can handle negative weight cycles. 