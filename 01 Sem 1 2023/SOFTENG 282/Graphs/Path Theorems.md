>[!info] Theorem 5.4
let $G = (V, E)$ be a graph and let $u, v \in V$. If there is a [[Navigating Graphs|walk]] from $u$ to $v$. then there is a path from $u$ to $v$
>**Proof:**
>- Let $P = v_{0} , 𝑣_{2} , 𝑣_{3} , \dots ,𝑣_{n}$ be the shortest walk from 𝑢 to 𝑣  
>- Assume $P$ is not a path, then $\exists i \neq j$ such that $v_{i} \neq v_{j}$
>- We can construct a walk $Q$ that is strictly shorter than $P$ 
	>	- $Q  = v_{0} , 𝑣_{1}, \dots ,𝑣_{j}, v_{j+1}, \dots , v_{n}$  
>- Contradictory to $P$ as the shortest walk  
>- P cannot be shortest walk and non-path at the same time  
>- Any shortest walk must be a path  
> ![[Pasted image 20230508163714.png | centre | 200]]

>[!info] Theorem 5.5
>Let $G = (V, E)$ be a graph and let 𝑢, 𝑣, 𝑤 ∈ 𝑉. If there is a walk from 𝑢 to 𝑣 and a  
walk from 𝑣 to 𝑤 then there is a walk from 𝑢 to 𝑤.

### Connectedness
An undirected graph is called connected if there is a walk between every pair of distinct vertices on the graph. 

>[!Example]-
>$G_1$ is connected
>$G_{2}$ is not conected
>$G_{3}$ is connected
>![[Connected graph examples.png | center| 500]]
>

>[!info] Theorem 5.6
>There is a path between every pair of distinct vertices of a connected [[Types of Graphs#Undirected Graphs|undirected graph]]
>**Proof**:
>*Definition*: Connected suggests that there is a walk from $u$ to $v$ for every pair $u, v$
>*Theorem*: Suggested by theorem 5.4, there is a path from $u$ to $v$ for every pair $u, v$

#### Connectivity Equivalence 
If there is a walk from x to y then there is a path from x to y. 
![[Undirected Graphs Connectivity Exersise.png]]

#### Connected Components
A connected component of graph $G$ is a connected subgraph of $G$ that is not a proper subgraph of another connected subgraph of $G$
A graph that is not connected has two or more connected components that are disjoint and have $G$ as their union. 

>[!Example]-
>The graph $H$ is the union of three disjoint subgraphs $H_{1}, H_{2}, H_{3}$
>These subgraphs are the connected components of $H$
>![[Connected Components Example.png| center | 400]]
>


#### Connectedness in Directed Graphs
A Directed graph is **Strongly Connected** if there is a path from $u$ to $v$  and a path from $v$ to $u$ whenever $u$ and $v$ are vertices in the graph

A graph is weakly connected if there is a path between every two vertices in the [[Graph Terminology#Underlying graph|underlying undirected graph]] by ignoring the directions of the edges of the directed graph. 

>[!Example]
>![[Example, Strong and Weak Connections.png]]