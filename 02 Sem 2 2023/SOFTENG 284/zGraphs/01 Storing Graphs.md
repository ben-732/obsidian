
### Storing a digraph
A computer doesn't really know what a graph is, we have to find a representation of the graph.

There are two main types of representation for [[Types of Graphs#Directed Graphs|digraphs]]. 
#### Adjacency Matrix
The adjacency matrix $A_{g =  [a_{{ij} }]}$ of $G = (V, E)$ with respect to the listing of vertices $v_{1}, v_{2}, \dots, v_{n}$ is the $n\times n$ zero-one matrix with the following settings:

![[Pasted image 20230913131256.png  | center  | 500]]

#### Adjacency List
List the matrix but lists adjacent nodes. 
![[Pasted image 20230913131354.png]]

#### Two graphs in one file
We can store several digraphs one after the other in a single file as follows: 

- Use one line to store the order at the beginning of each digraph.  
- If the order is $n$ then the next 𝑛 lines give the **adjacency matrix** or **adjacency lists** representation of the digraph. If we use numeric labels, node labels can be omitted.  
- The end of the file is marked with a line denoting a digraph of order 0.  
![[Pasted image 20230913132243.png | center | 400]]
#### Other methods
We have already seen a way to represent a binary heap as the array. Here is a way of  
storing a general tree in an array.  
• A general rooted tree of 𝑛 nodes can be stored in array 𝑝𝑟𝑒𝑑 of size 𝑛.  
• `pred[i]` is the parent of node 𝑖.  
• The root has no parent, so assign it 𝑛𝑢𝑙𝑙 or -1 if we number nodes from 0 to 𝑛−1 in  
the usual way.  
• This is a form of adjacency lists, using in-neighbours instead of out-neighbours.  

![[Pasted image 20230913132519.png | center | 500]]

#### Implementation of digraph ADT
- An adjacency matrix is simply a matrix which is an array of arrays.  
- Adjacency lists are a list of lists.  
- There are several ways in which a list can be implemented, for example by an array, or singly- or doubly-linked lists using pointers.  
- Depending on an implementation certain operations may have different running time.  


#### Adjacency List or Matrix?
Depends on three factors governing storage requirement and performance:  
- The order of the (di)graph $|𝑉(𝐺)|$: the storage space we require for an adjacency matrix  
is $Θ(𝑛 ! )$.  
- The size of the (di)graph $|𝐸(𝐺)|$: the storage space we require for an adjacency list is  
Θ(𝑛 ! ). However, in most practical cases, the graphs are sparse. E.g., if the in-/out-degree  
is limited, the space requirement is only $Θ(𝑛 + 𝑒)$, where $𝑒$ is the number of edges.  
- What we want to do with the graph (operations). This requires a bit more discussion.  
