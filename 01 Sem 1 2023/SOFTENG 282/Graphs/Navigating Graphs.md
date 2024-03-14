# Walk
A walk of length $n$ from $u$ to $v$ in an [[Types of Graphs#Undirected Graphs|undirected graph]] $G$ is a sequence of edges $e_{1}, \dots e_{n}$ of the graph.

$$
f(e_{1}) = \{ v_{0}, v_{1} \}, f(e_{2}) = \{ v_{1}, v_{2} \}, \dots, f(e_{n}) = \{ v_{n-1}, v_{n} \}
$$
Where $v_{0} = u$ and $v_n = v$

When $G$ is a simple graph, we denote this path by its vertex sequence $v_{0}, v_{1}, \dots , v_{n}$

# Cycle
A circuit is a cycle if each vertex, except for the first/last one, only appears once
>A cycle must involve at least three places

$a, b, c, a$ is a cycle of length $3$

![[path definition example.png| center]]

# Path
A walk is a path if it does not contain the same vertex more than once

![[path definition example.png| center]]
$a, c, b, d$ is a path of length $3$

>[!Exercise] Exercise: Walks, Paths and Cycles
>![[Exercise- Walks, Paths and Cycles.png]]

| Sequence | Walk? | Path? | Cycle? |
| --- | ---| ---- | --- |
|$a-b-c$ | yes | yes | no |
| $e-g-e$ | yes | no | no |
|$d-b-c-d$ | yes | no | yes|
|$d-a-d-f$ | yes | no | no
|$a-b-d-f-h$ | yes | yes| no|

## Navigating Directed multigraphs
#### Walk
A walk of length $n$ from $u$ to $v$ in a directed graph $G$ is a sequence of edges $e_{1}, \dots, e_n$ of the graph s.t.
$$
f(e_{1}) = \{ v_{0}, v_{1} \}, f(e_{2}) = \{ v_{1}, v_{2} \}, \dots, f(e_{n}) = \{ v_{n-1}, v_{n} \}
$$
where $v_{0} = u$ and $v_{n} = v$

When $G$ has no multiple edges, this path is denoted by its vertex sequence $v_{0}, v_{1}, \dots v_{n}$

#### Path
A walk is called a path if it does not contain the same vertex more than once. 

#### Cycle
If a walk shares no vertices except for the first/lasts element which is the same

#### Circuit
A walk that starts and end at the same vertex is called

>[!Example] Exercise: Walks, Paths, And cycles
>![[Example, Directed multigraphs naviagtion.png]]


