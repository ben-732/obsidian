## DAGs
### Detecting DAGs 
- Suppose that there is a cycle in $G$ and let $v$ be the node in the cycle visited first by DFS. If $(u, v)$ is an arc in the cycle then it must be a back arc.  
- Conversely, if there is a back arc, we must have a cycle.  
- Suppose that DFS is run on a digraph $G$. Then $G$ is acyclic if and only if $G$ does not contain a back arc.  
- A digraph with no cycle is called a **directed acyclic graph** (**DAG**).

![[Pasted image 20231010155224.png | center | 300]]
#### Detecting Cycles in Digraphs
Once DFS finds a cycle, the stack contains the nodes that form the cycle
![[Pasted image 20231010155327.png | center | 400]]
### DAG facts
- Any walk on a DAG 𝐺 is limited in length  
- A DAG must have at least one source and one sink (why?)  
	- A graph that has no source or no sink must have a cycle  
- Although a DAG has no cycles the [[Graph Terminology#Underlying graph|underlying graph]] could have cycles.  


## Topological Sorting
- A topological sorting of a digraph $G$ is an ordering on its vertices such that, for each arc $(u, v)$ of $G$, $u$ appears before $v$ in the ordering.  
- To place nodes of a digraph on a line so all arcs go in one direction.  
-  Main application: scheduling events (arithmetic expressions, university prerequisites, etc)
![[Pasted image 20231010155627.png | center | 400]]
- If $G$ is a DAG, then there is at least one topological order of the vertices.  
- A topological order is a numbering of the vertices such that an arc $(u, v)$ in the digraph  means that $u$ has a smaller number than $v$.  
- **Only DAGs** have a topological ordering.  


- There is often more than one topological order for a digraph 
- A topological order is also called a topological sort or a linear order. Because we can order the vertices in a line and make all arcs point the same way  
![[Pasted image 20231010155831.png | center | 400]]

![[Pasted image 20231010160139.png | center | 500]]

### Finding Ordering 
There are two easy ways of finding a topological ordering in a DAG
1. List of finishing times by DFS, in reverse order (since there are no back arcs, each node finishes before anything pointing to it).  
2. Zero in-degree sorting – Find a node of in-degree zero, delete it and repeat until all nodes listed.  

#### DFS method
Go through with a standard DFS search and store the order that elements are `pop()`ed from the stack. 
![[Pasted image 20231010160727.png  | center | 400]]

#### Zero In-degree
Start with DAG, say $G$. Initialise the order list $L$ as empty.  

1. Find a source vertex $v$.  
2. Delete $v$ and all its outgoing arcs. Append node $v$ to the ordered list $L$. Since we only delete arcs and vertices, this process does not create a cycle. Hence, the resulting graph is a DAG and has at least a source node.  
3. Repeat 1 and 2 under all the vertices that have been deleted. Then, we will get a  topological order 𝐿 of 𝐺.

##### Running time
The running time of a naive implementation of zero in-degree sorting where a source is found and then removed at each step. How could this idea be made more efficient? 

- For each iteration, we need to find zero in-degree vertex $v$, delete $v$ and its out-going arcs. We calculate the in-degree over every node each time, it takes
	- Adjacency Matrix: $O(n^2)$
	- Adjacency List: $O(m)$  
- For all $n$ iterations:  
	- Adjacency Matrix: $O(n³)$
	- Adjacency List: $O(nm)$
- We don’t have to calculate the in-degree every time, we can use an array to track the  in-degree of every node!  

##### Faster Algorithm
This algorithm keeps track of in degree in an array and does cool stuff w it
![[Pasted image 20231010161308.png | center | 400]]

### Exercise ?
Topological orders are not unique. List all possible topological orders of the following  
digraph. We can use zero in-degree sorting.

![[Pasted image 20231010161548.png | center | 400]]