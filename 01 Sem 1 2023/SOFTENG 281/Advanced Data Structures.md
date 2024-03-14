Mathematical data structures that are useful in programming

## Graphs
A graphs is a tuple that consists of a set of vertices and a set of edges
$G \lt V, E\gt$

### Edges
An edge $E_n$ is a connection between two vertices
Every edge is a member of the cross product of $V$ ($V\times V$). This means that $E$ is a subset of the cross-product of V $E \subseteq V \times V$

## Properties
- Recurrence Relation properties
	- Reflexive: $\forall v\in V, (v, v) \in E$
	- Symmetric: $(v_1, v_2) \in E \to (v_2, v_1) \in E$
	- Antisymmetric: $(a = b \bigwedge b =  a) \to a = b$
	- Transitive: $\text{if } (v_1, v_2) \in E,\, \text{ and } (v_2, v_3) \in E,\, \text{ then } (v_1, v_3) \in E$
	- Equivalence relation: Reflexive, Symmetric, and Transitive
- Equivalence Class $[v_{1}] = \{v2‖v2∈V (v_1, v_2) \in E \}$


## Sets
Sets are like the mathematical set. They are an unordered list of unique objects. 

### Set Notation

#### Union
Elements in set A or in set B
$$
 A\cup B = \{ x \mid x \in A \text{ or } x \in B \} 
$$
#### Intersect
Elements that are in BOTH set A and B
$$
 A\cap B = \{ x \mid x \in A \text{ and } x \in B \} 
$$
#### Subset 
==All Elements that are ==
$$
A \subset B = \{ x \mid x \in A  \}
$$

#### Difference
All Elements of A that are not in B
$$
A \backslash  B = \{x \mid x \in A, x \notin b \}
$$
#### Power Set
All possible subsets of a set A
$$
P(A) = \{ \varnothing, \{ A_{1} \}, \{ A_{2} \}, \{ A_{1}, A_{2} \} \}
$$
#### Cross Product
All possible tuples from a combination of two sets. 

$$
A \times B = \{ (x, y) \mid x \in A \text{ and } y \in b \}
$$
$$
A \times B = \{ (A_{1}, B_{1}), (A_{1}, B_{2}), (A_{2}, B_{1}), (A_{2}, B_{2}) \}
$$



### The Set Interface
A `Set` is a `Collection` of unique items. Thus, sets have **no duplicate elements**. Also, by definition, they are **unordered**.

It has many useful APIs:
```java
// Create a set of type Integer
Set<Integer> numbers = new HashSet<Integer>();

// Add a member into a set:
numbers.add(520);

// Check if a set contains an element:
numbers.contains(1314);

// Check if two sets contain exactly the same elements
setA.equals(setB);
```

