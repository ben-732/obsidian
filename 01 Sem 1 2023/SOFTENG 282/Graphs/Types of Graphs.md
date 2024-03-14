## Undirected Graphs
### Simple Graph
A simple graph consists of $G = (V, E)$
- $V$ - A nonempty set of vertices
- $E$ - A set of unordered pairs of distinct elements of $V$ called edges. 

![[Simple Graph.png|300]]
This is a simple graph with four vertices and five edges
-  $V = \{a,b, c, d\}$
-  $E = \{\{a, b\}, \{a, c\}, \{b, c\}, \{b, d\}, \{c, d\}\}$

### Multigraphs
A multigraph $G = (V, E)$ may have multiple edges connecting the same two vertices

When $m$ different edges connect the vertices $u$ and $v$, we say that $\{u, v\}$ is an edge of multiplicity $m$

![[MultiGraph.png|400]]

### Pseudograph
A Pseudograph $G = (V, E)$ may include loops as well as multiple edges connecting the same pair of vertices. 

![[PsuedoGraph.png|300]]

- $V = \{a, b, c\}$
- $E = \{e_1, e_2, e_3, e_4, e_5, e_6, e_{7} \}$
- $E = \{\{ a, b \}, \{  a, c\}, \{ b, c \}, \{ b, b \}, \{ c, c \}  \}$

## Directed Graphs
### Directed Simple Graph

A Directed Graph (Diagraph) $G = (V, E)$ consists of 
- $V$ - a nonempty set of vertices (or nodes)
- $E$ - a set of ***directed*** edges (or arcs)

Each edge is associated with an ordered pair of vertices
The directed edge associated with the pair $(u, v)$ is said to start at $u$ and end at $v$
Obviously, $(u, v) \neq (v, u)$ 

![[Directed  Simple Graph example.png|200]]

### Directed Multigraph
A directed multigraph may have multiple directed edges . 
when there are $m$ directed edges from the vertex $u$ to $v$, we say that edge has a multiplicity of $m$

![[Directed Multigraph Example.png|200]]
Here, the multiplicity of $(a, b) = 1$ while $(b, c) = 2$

### Directed Pseudograph
A directed pseudo graph $G = (E, V)$ consists of 
- $V$ - a set of vertices
- $E$ - a set of edges
- $f: E \to \{ (u, v) | u, v \in V   \}$

The edges $e_1$ and $e_2$ are multiple edges if $f(e_1) = f(e_2)$

![[Directed Pseudograph Example.png| 300]]


### Summary

| Type  |  Edges |  Multiple Edges | Loops |
| ---- | - | - | -|
| Simple Graph | Undirected | No | No|
| Multigraph | Undirected | Yes | No |
| Pseudograph | Undirected | Yes | Yes |
| Simple Directed Graph | Directed | No | No |
| Directed Multigraph | Directed| Yes | No | 
| Directed Pseudograph | Directed | Yes | Yes|
| Mixed graph | Both | Yes | Yes|


