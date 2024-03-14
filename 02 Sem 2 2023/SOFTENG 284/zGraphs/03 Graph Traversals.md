In graphs we want to be able to:
- Visit each node of a digraph in a systematic and efficient way (e.g., to search a graph).  
-  Walk only on arcs following their direction  


### Colour Scheme Traversal
All graph traversal algorithm follow the same structure which is called the general graph traversal algorithm. This algorithm uses three types of nodes:  
- White nodes: have not yet been visited.  
- Grey (frontier) nodes: have been visited but may have adjacent nodes that are white.  
- Black nodes: have been visited and all their (out-) neighbours have been visited as well
![[Pasted image 20230914131557.png | center | 300]]

#### Algorithm
- All nodes are white to begin with.  
- A starting white node is chosen and turned grey.  
- A grey node is chosen and its out-neighbours explored.  
- If any out-neighbour is white, it is visited and turned grey. If no out-neighbours are white, the grey node is turned black.  
- The process of choosing grey nodes and exploring neighbours is continued until all nodes reachable from the initial node are black.  
- If any white nodes remain in the digraph, a new starting node is chosen and the process continues until all nodes are black.  

>[!tip]- Visits
>1. `s` is coloured grey and `pred[s] = null`.  
>2. choose a grey node `u`.  
>3. if `u` has a white (out)-neighbour `u` then colour `v`  grey and `pred[v] = u` else colour `u` black.  
>4. if we have grey nodes go to step (2).  
>
> Pred here means predicesor
> ![[Pasted image 20230914131932.png | center | 200]]
> ![[Pasted image 20230914132056.png | center | 400]]


![[Pasted image 20230914132335.png | center | 500]]
### Search Forests
A search forest is a collection of node-disjoint trees that span the digraph and  
contain, for each node $u$ with `pred[u] != null`, the arc `(pred[u], u)`.
![[Pasted image 20230914134232.png | center | 200]]

### Arc Classifications
- Suppose we have performed a traversal of a digraph $G$, resulting in a search forest $F$. Let  $(u, v) \in E(G)$ be an arc.  
- The arc is called a tree arc if it belongs to one of the trees of 𝐹. If the arc is not a tree arc,  there are three possibilities:  
	- A forward arc if 𝑢 is an ancestor of 𝑣 in 𝐹,  
	- A back arc if 𝑢 is a descendant of 𝑣 in 𝐹, and  
	- A cross arc if neither 𝑢 nor 𝑣 is an ancestor of the other in 𝐹.  
![[Pasted image 20230914133704.png | center | 500]]
![[Pasted image 20230914133838.png | center | 400]]

### Traversal Trees
>[!info] Theorem
>![[Pasted image 20230914134501.png | center | 600]]

>[!info] Theorem 2
>![[Pasted image 20230914134615.png | center | 600]]

>[!info] Theorem 2 cont or 3
>![[Pasted image 20230914134718.png | center | 600]]


### Traversal Complexity 


## Depth First search (DFS)

## Breadth First search (BFS)

## Priority First search (PFS)
Uses [[7. Data structures#Priority Queue |Priority Queue]] data structure to visit nodes based on their numerical order rather than proximity to the root. 

Does cool things
