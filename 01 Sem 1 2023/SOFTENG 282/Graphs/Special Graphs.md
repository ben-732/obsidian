## Special Types of [[Types of Graphs#Simple Graph|Simple Graphs]]
### Complete graph
A Complete graph on $n$ vertices, denoted by $K_n$ is the simple graph that contains exactly one edge between each pair of distinct vertices. 

![[Simple Graphs.png]]

- $|V|$ in $K_n$ : $n$
-  $|E|$ in $K_n$: $\large\frac{n(n-1)}{2}$

> Another name is a "clique" (apparently idk how much i trust that information)
### Cycle
A Cycle $C_n$ for $n \geq 3$ consists of $n$ vertices $\large v_1, v_2, ..., v_n$ and edges $\large \{ v_{1}, v_{2} \}, \{ v_{2}, v_{3} \}, \dots, \{ v_{n-1}, v_{n} \}, \{ v_{n}, v_{1} \}$
![[Pasted image 20230505220411.png | centre]]

- $|V|$ in $C_n$ : $n$
- $|E|$ in $C_n$: $n$

### Wheel
A wheel $W_n$ is obtained by adding an additional vertex toa cycle $C_n$ for $n \geq 3$ and connecting this new vertex to each of the $n$ vertices in $C_n$ by new edges

![[Wheel Graphs Examples.png|centre]]

- $|V|$ in $W_n$ : $n + 1$
- $|E|$ in $W_n$: $2n$

### $n$-dimensional Hypercube ($n$-cube)
A graph with $2n$ vertices representing all bit strings of length $n$, where there is an edge between two vertices, they differ by exactly one bit position
![[Hypercube Graphs Examples.png|centre]]
- $|V|$ in $Q_n$ : $2^n$
- $|E|$ in $Q_n$: $n\times 2^{n-1}$ (This can be proved through induction)

### Bipartite
A graph where $V$ can be split into two subsets $V_1$ and $V_2$ such that every edges connects a vertex in $V_1$ and $V_2$ - edges only exist between $V_1$ and $V_2$ 
![[Bipartite Graph example.png|centre]]
![[Bipartite Example 2.png| centre]] ^4383c5

>[!Exercise] Exercise: Show that cycle $C_3$ is not bipartite
>- If we decide that the vertex set of $C_3$ into two nonempty sets, one of the two must contain two vertices. 
>- $C_3$ every vertex is connected to every other vertex, Therefore the two vertices in the same partition are connected. Hence, $C_3$

#### Complete bipartite
Like a regular bipartite graph except every vertex in $V_1$ is connected to every vertex in $V_2$ 

![[Complete bipartite graphs examples.png | center]]
- $|V|$ in $K_{m, n}:m+n$
- $|E|$ in $K_{m, n}:m\times n$

>[!Exercise]
>**Question:** How many edges can a bipartite graph with 7 vertices have?
>**Solution:**
>$$ K_{1, 6}: 1 \times 6 = 6 \text{ edges} $$
>$$ K_{2, 5}: 2 \times 5 = 10 \text{ edges} $$
>$$ K_{3, 4}: 3 \times 4 = 12 \text{ edges} $$
>


