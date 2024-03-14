### Adjacent/Neighbours
Two vertices $u, v$ in an undirected graph $G$ are called adjacent or neighbours if there is an edge $e$ between $u$ and $v$

Such an edge $e = \{ u, v \}$ is called *incident* with vertices $u$ and $v$ 
$e = \{ u, v \}$ is said to connect $u$ and $v$

>[!Example]-
> ![[ Pasted image 20230504142519.png |centre| 300]]
> - $a$ and $b$ are adjacent in $G$
> - $\{ a, b \}$ is incident with vertext $a$ and $b$
> - $\{ a, b \}$ connects vertex $a$ and $b$

### Degree
The degree of a vertex in an undirected graph is the number of edges incident with it. 

>A loop at a vertex will contribute **Two** to the degree of that vertex

The degree of the vertex $v$ is denoted by $deg(v)$.

We can describe a graph using the degree sequence: $= [n_{0}, n_{1}, n_{2}, ..., n_{n}]$
$n_i$ is the number of nodes of degree $i$

>[!Example]-
>What are the degrees of the verticies in the graphs $G$ and $H$?
>![[Graph Degree Example.png]]


### Handshaking Theorem
Let $G = (V, E)$ be an undirected graph with $|E|$ edges. Then:
$$
2|E| = \sum_{v\in V}{deg(v)}
$$

This comes from the idea that each edge is made up of two "half edges" one from each vertex the edge connects to.  Therefor the degree of the graph is going to be equal to two times the number of edges. 

![[Handshaking Scribble.png | centre]]

>[!Example]-
>*How many edges are there in a graph with 10 vertices each of degree 6*
>$$6 \times 10 = 60$$
>$$ 2|E| = 60$$
> $$ |E| = 30 $$
>
> So this tells us that the number of edges in our graph is $30$

This links to degree sequences as we know that a graph must overall have twice as many edges as its total degree. 

>An undirected graph has an **even** number of vertices of **odd** degree

**Proof:**
Let $V_{1}$ be the vertices of even degree and $V_{2}$ be the vertices of odd degree in an undirected graph
$$
\large\begin{align}
\underbrace{ 2|E| }_{ \text{Even} } &= \sum_{v\in V} deg(v) \\
&= \underbrace{ \sum_{v\in V_{1}} deg(v) }_{ \text{Even} } + \underbrace{ \sum_{v\in V_{2}} deg(v) }_{ \text{this must be even} }
\end{align} 
$$


### In-degree and Out-degree
- The in-degree  $\text{deg}^-(v)$ and is the number of edges that terminate at $v$
- The out-degree $\text{deg}^+(v)$ is the number of edges with $v$ as their initial vertex
- A loop contributes 1 to both totals. 

>[!Example]-
>![[Pasted image 20230505213951.png]]

>[!info] Theorem 5.3
>Let $G = (V, E)$ be a graph with Directed edges then the total edges will be equal to the sum on in-degrees, and the sum out out-degrees
>$$\large |E| = \sum_{v\in V} \text{deg}^+(v) = \sum_{v\in V} \text{deg}^-(v)$$



### Underlying graph
The underlying graph of a [[Types of Graphs# Directed Simple Graph|Digraph]] $G = (V, E)$ is the graph $G^{\prime} = (V^{\prime}, E^{\prime})$ where:
$$ \large E^{\prime} = \{ \{ u, v \} | (u, v) \in E \}$$
![[Pasted image 20230505215055.png | centre]]

### Subgraph
A subgraph of graph $G = (V, E)$ is a graph $(W, F)$, where $W \subseteq V$ and $F \subseteq E$
A subgraph $H$ of $G$ is a proper subgraph of $G$ if $H \neq G$
$K_{5}$ and one of its subgraphs

![[Subgraph Example.png|centre]]

### Union of graphs
The union of two simple graphs $G_{1} = (V_{1}, E_{2})$ and $G_{2} = (V_{2}, E_{2})$ is the simple graph with vertex set $V_{1} \cup V_{2}$ and edge set of $E_{1} \cup E_{2}$. The union of $G_{1}$ and $G_2$ is denoted by $G_{1}\cup G_{2}$.

![[Union of graphs example.png | centre]]

### Isomorphism of  Graphs
Two graphs are said to be isomorphic if there is a [[Functions#Bijective Graphs|bijective function]] (one-to-one and onto) $f: V_1 \to V_{2}$ with the property that $a$ and $b$ are adjacent in $G_1$ if and only if $f(a)$ and $f(b)$ are adjacent. 

The function $f$ is called an *isomorphism*

If you can rearrange graph $G$ to get graph $H$ (Moving/renaming vertices), they are isomorphic

>[!Example]-
>![[Isomorphic Example.png]]

The function is bijective, but it might not be unique, there may be another function that also works.  like in this example. 

#### Invariant Properties
There are $n!$ possible one-to-one correspondences between the vertex sets of two simple graphs with $n$ vertices. 

Its usually difficult to determine whether two simple graphs are isomorphic. Because of this, we check several invariant properties that are necessary for two graphs to be isomorphic but **not sufficient** in of themselves. 

1. Isomorphic simple graphs must have the same number of vertices (1-1 and onto)
2. Isomorphic simple graphs must have the same number of edges (1-1 and onto)
3. The degrees of the vertices in isomorphic simple graphs must be the same (same degree sequence)

>[!example]-
>Graphs $G$ and $H$ are NOT Isomorphic
>![[Isomorphism Invariant properties example.png]]

You can also determine isomorphism by using techniques like checking the degree of neighbours. If a vertex of degree 2 has two neighbours of degree 3, the same pattern has to appear in an isomorphic graphs. 

Another thing you can check for is Cycles of any length which must appear in both  graphs. 






### Euler Paths and Circuits
- An Euler Circuit in a graph $G$ is a simple circuit containing every edge of $G$
- An Euler Walk in a graph $G$ is a simple Walk containing every edge of $G$

>[!Example]-
>![[Euler Walks and Circuits  Example.png | center | 600]]

#### Necessary and Sufficient Conditions
An **Euler circuit** begins with a vertex $a$ and continues with an edge incident with $a$, say $\{ a, b \}$. The edge $\{ a, b \}$ contributes 1 to $\text{deg}(a)$. Each time the circuit passes through a vertex, it contributes two  to the vertex's degree.  When the circuit terminates where it started, it adds 1 to the start degree too. Therefore $deg(a)$ must be. Therefore in a graph with an Euler Circuit, the degree of every vertex must also be **even**.
![[Euler Circuit Example.png| center | 500]]

#### The other way around...

>[!info] Theorem 5.8
> A connected multigraph has an Euler Circuit iff each of its vertices have an even degree.

For a graph $G$, prove existence of  Eulers circuit

- Find a circuit $C$ in $G$ (or $H$)
	- Every Edge is traversed once
	-  Each edge has an even degree
- Graph $H = G\setminus C$

Repeat till $H$ is empty $\varnothing$
When you join all the small circuits, you get one big circuit where all edges are traversed once. 

> [!edit]- Pseudocode
> ![[Eulers circuit finding algorith.png]]

>[!Example]-
Look for small circuits that combined together make a valid Euler's circuit. 
![[Euler circuit finding example.png| center | 400]]

>[!info] Theorem 5.9
>A connected multigraph has a Euler Walk iff it has exactly two vertices with an odd degree. 

Proof: Same as last but the odd vertices must be the start/end point. 

// the start and end vertices use 1 degree for the start/end. Leaving even vertices. 

### Hamilton Paths / Cycle
Hamilton paths are like Euler paths except you visit every **Vertex** once instead of every edge

There is no easy way of doing this

