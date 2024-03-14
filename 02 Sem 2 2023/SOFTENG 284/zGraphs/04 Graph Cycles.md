A cycle is effectively a loop in a graph. In a digraph the direction matters

![[Special Graphs#Cycle]]

### Cycle Detection 
- Suppose that there is a cycle in 𝐺 and let 𝑣 be the node in the cycle visited first by DFS. If (𝑢, 𝑣) is an arc in the cycle then it must be a  back arc.  
- Conversely, if there is a back arc, we must have a cycle.  
- Suppose that DFS is run on a digraph 𝐺. Then 𝐺 is *acyclic* if and only if  𝐺 does not contain a back arc

### Girth and Directed girth
- **Definition**. For a graph (with a cycle), the length of the shortest cycle is called  
the girth of the graph. If the graph has no cycle then the girth is undefined, can  
be viewed as +∞.  
- **Convention**. For a digraph we use the term girth for its underlying graph and  
the term directed girth for the length of the smallest directed cycle.  
![[Pasted image 20231008142551.png | center | 500]]
### Finding the girth 
DFS is not a reliable algorithm for finding girth, however Cycles can be easily detected in a graph using BFS. Finding a cycle of minimum length in a graph is not difficult using BFS (better than DFS).
![[Pasted image 20231008142805.png | center | 400]]

- Perform `BFSVisit()` $|V|$ times, starting at each vertex $v^\prime \in V$ in turn.  
- If during a BFSVisit, we encounter a grey neighbour (rather than a white) we have found a cycle  
- The grey node was visited before following another path  
- An important property of BFS is that if it runs from vertex $v$, then every vertex $s$, when it is first reached, then the path that was found from $v$ to $s$ is minimal.  Thus, reaching $v’$ from $v$ with BFS finds the shortest path from $v$ to $v’$, namely the shortest cycle that contains $v$.

#### Shortest Path
Because of the swag way bfs works:

- Let  ${u, w}$ be a cross edge in a tree. Then either $d[u] = d[w]$ or $d[u] - d[w] = 1$  

Sketch of proof: Suppose `d[u] - d[w] > 1`

![[Pasted image 20231008143637.png | center | 500]]

### Cycle length trivia :0
- If vertex $v$ is on at least one cycle then BFS starting at $v$ will find it.  
- On detection of a cross edge between descendants $u$ and $w$, determine whether $u$ and $w$ are in different subtrees below $v$ (the root of the tree).  
	- If yes, then a cycle of length `d[u] + d[w] + 1` is found.  
	- If no, then a cycle of shorter length is found (but avoids 𝑣).  
• If `d[u] = d[w]` then odd length, where 𝑣 is a common ancestor.  
• Otherwise, `d[u]+1=d[w]` and even length.

### Finding girth
#### Undirected Graph
To compute girth, we perform `BFSVisit(v)` procedure once for each $v \in V(G)$ and take minimum.  

• If a grey neighbour is met, e.g., an edge $(u, w)$ is explored from $u$ where $w$ is grey, continue to the end of the current level and then stop.  
• For each edge $(u, w)$ as above on this level, if 𝑣 is the lowest common ancestor of 𝑢 and 𝑤 in the BFS tree, then there is a cycle containing $u, w, v$ of length  $l = d(u) + d(w) + 1$
• Report the minimum value of $l$ obtained along the current level.  

• The minimum of these lengths at the level is the smallest cycle that involves 𝑣  
• The smallest cycle among all possible start vertices 𝑣 is the girth.

#### Digraph
Following procedure finds the (length of the) shortest directed cycle through node 𝑣 in a  
digraph.  
1. Run `BFSVisit(v)` starting at node $v$.  
2. The first time a back-arc of the form (𝑥, 𝑣) is found, we have found a cycle of length equal to (1 + the depth of 𝑥 in the search tree).  
3. Stop and Return the length found.  

- Run the above procedure on every node and pick the length of the shortest cycle  
