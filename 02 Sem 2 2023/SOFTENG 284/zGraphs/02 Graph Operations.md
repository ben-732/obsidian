## 7 Elementary Operations
1. Does the arc $(u, v)$ exist?  
2. What is the outdegree of vertex $u$?  
3. What is the indegree of vertex $u$?  
4. Add an arc between two vertices 𝑢 and 𝑣.  
5. Delete the arc between vertices 𝑢 and 𝑣.  
6. Add a node to the (di)graph.  
7. Delete a node from the (di)graph.  

### Operations
#### 1: Arc
![[Pasted image 20230913133121.png | center | 300]]
Matrix: find row $i$, col $j$  -  $\Theta(1)$
List: lookup row $i$ and search for $j$ in row $i$.   worst case $\Theta(d)$
#### 2: Out degree
Matrix: Count the ones in the row
List: Get the length of the list
#### 3: In degree
Matrix: Scan column in each row and count 1s $\Theta(n)$
List: Scan all sequences and count occurrences of vertex  $\Theta(n + e)$

#### 4: Add arc
Matrix: Put a 1 in position $(i, j)$    - $\Theta(1)$
List: Add vertex to end of list at position   -   $\Theta(1)$

#### 5: Remove
Matrix: Add but set to 0 instead of 1   - $\Theta(1)$
List: Get list and find node and remove it    - $\Theta(d)$

#### 6: Add node
Matrix: Create entire empty row and column and add them.  -  $\Theta(n)$
List: Create a new list for node 7  -  $\Theta(1)$

#### 7: Remove node

Matrix Remove a row and column, shifting rows and columns up and to the left. A simpler way to do this is to copy the elements to a new matrix of size $(n-1)^2$  --     $\Theta(n^2)$

List: Remove list for node and check through every other list and remove references to original list    -   $\Theta(n + e)$

### Summary
>[!faq]+ Operations
>![[Pasted image 20230913134837.png | center | 500]]

>[!faq]+ Complexities
>![[Pasted image 20230913134854.png | center | 500]]

### Space requirements
- The adjacency matrix representation requires $\Theta(n^2)$ storage as we simply need a matrix of $n^2$ bits.  
- The adjacency list space requirement is $\Theta(n + e\log n)$.  
- We need $n$ lists and the combined length of all the lists is $e$.  
- Node numbers require more than one bit of storage each; the number 𝑘 uses about $\Theta(\log k)$ bits.  
