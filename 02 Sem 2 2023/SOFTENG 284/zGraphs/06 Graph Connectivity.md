A Graph $G$ is **connected** if for each vertex $u \in V(G)$, there is a path to any other vertex $v \in V(G)$ 

A Graph $G$ is then **disconnected** if it is not possible to get to every other vertex, and induced connected subgraphs are called the *components* of $G$.

## Connected Components
Any graph $G$ consists of one or more subgraphs $G_i$ such that:
- Each $G_{i}$ is connected 
- If there is a path between $u, v \in V(G)$ then $u$ and $v$ are in the same subgraph $G_{i}$

These subgraphs are the connected components of $G$
![[Pasted image 20231010163052.png | center | 400]]

### Finding connected Components
Let $G$ be an undirected graph and suppose BFS or DFS is performed on $G$. Then the connected components of G are precisely the subgraphs spanned by the trees in the [[03 Graph Traversals#Search Forests |search forests]].

## Digraph Connectivity
A digraph $G$ is [[Path Theorems#Connectedness in Directed Graphs |strongly connected]] if for each pair of vertices, $u, v \in V(G)$ are mutually reachable - There is a path from $u$ to $v$ and from $v$ to $u$. A digraph is weakly connected if its underlying graph is connected. 

The maximal sub-digraphs induced by mutually reachable nodes of a digraph are called the strong components of $G$

> A strongly connected digraph of order at least two contains at least one loop

![[Pasted image 20231010164130.png | center | 400]]

### Finding Components in digraphs 
Using just simple BFS or DFS to find strongly connected digraphs will often fail. This is because there are graphs where these methods will return a single root. An example of this is a tree. 

However BFS/DFS can be used to determine if a digraph is strongly connected by running the algorithm at each node and checking weather the graph returns a single tree or not. 
![[Pasted image 20231010164541.png | center | 500]]

>[!info] Observations
  Observation 1:
>- Consider a digraph $G$ and its reverse digraph $G^{\prime}$.>
>- The strongly connected components are the same subsets of vertices in both digraphs.>
>
>Observation 2:
>- Consider "shrinking" the connected components down to a single vertex in both digraphs.
>- This gives us $H$ from $G$ and $H_r$ from $G_{r}$.

This means we need to make sure each tree in the search forest corresponds to one strong component. Therefore the order of choosing starting nodes of DFS matters!

![[Pasted image 20231010165329.png | center |600]]
#### Component Graph
Component graph:
- Let each strong component be a vertex
- Maintain an arc from one strong component $C$ to the other $C^{\prime}$ ,If there is an arc (𝑢, 𝑣), where $u\in C, v\in C^{\prime}$

Let $C$ and $C^{\prime}$ be two distinct connected components. If there is an arc from $C \to C^{\prime}$ then there shouldn't be one from $C^{\prime} \to C$. A component graph is a [[05 Acyclic Graphs + Topological Orders#DAGs|DAG]].

![[Pasted image 20231010165950.png | center | 400]]

If we run DFS on a node from $C^{\prime}$ below, then we should get a search forest with two trees corresponding to the two strongly connected components. However, we don't know the component graph beforehand so we need another method for determining where to start.  

![[Pasted image 20231010170044.png | center | 400]]

#### Reverse Digraph
Digraph $G_{r}$ is the reverse of digraph $G$
![[Pasted image 20231010170723.png | center | 400]]

Because all edges are reversed, the strongly connected components remain the same but the component graph also gets reversed. 

![[Pasted image 20231010170717.png |center |400]]
##### Strong components in reverse graphs
If we run the DFS on the reverse digraph $G_{r}$, there are two cases in the resulting search forests:



1. Start DFS on some node $x$ in $C^{\prime}$: The starting node $x$ in $c^{\prime}$ will be the last one finished (with the greatest `done[𝑥]`) among all nodes in both $C$ and $C^{\prime}$
2. Start DFS on some node $y$ in $C$: All nodes in $C$ will be finished before the second run of DFS on some node $x$ in $C^{\prime}$. Thus, $x$ still has the greatest `done[𝑥]`
![[Pasted image 20231010171729.png | center | 600]]
#### Final Algorithm
Let $G$ be a digraph and $G_{r}$ be its reverse digraph obtained by reversing all arcs in $G$. The idea is to run DFS on a node of $G$, which finished last in the DFS of $G_{r}$.

**Phase 1:** Run DFS on $G_{}r$, to get depth-first forest $F_{r}$. For each node $x$ in $G_{r}$, let `done[𝑥]` be the time at which $x$ turned from grey to black.

**Phase 2:** Run DFS on $G$, when choosing a new root, choose a white node $x$ such that `done[𝑥]` is as large as possible. This gives a forest $F$.

