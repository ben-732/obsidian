
### Types of Record organising
Blocking factor: the average number of records per block in a file.

![[Pasted image 20240503201756.png]]

>[!important]
>We use unspanned organization by default in the future discussions.

### Blocking Factor
The average number of records per block in a file ($bfr$)

If we have file with $r= 300$  records, Block size $B=4096$ bytes with each record being $R=100$ bytes 

$$bfr=\lfloor \frac{B}{R}\rfloor = \lfloor\frac{4096}{100}\rfloor = 40$$
$$\text{\# of blocks} = \lceil \frac{r}{bfr} \rceil= \lceil \frac{300}{40} \rceil = 8$$

### Sequential File Organisation
Suitable for applications that require sequential processing of the entire file, The records in the file are ordered by a search-key

**Search:** Search for a record with $X=K$in a file with $b$ blocks ordered on $X$ .
- Sequential scan 
- Binary search (the location of the $i$-th record can be calculated)

In the from before with a $bfr=40$, there are 8 blocks, this means that the worst case scenario would require $\log_{2}b=3$ IOs to find the record. 

**Deletion:** use pointer chains

**Insertion:** locate the position where the record is to be inserted 
- If there is free space insert there 
- If no free space, insert the record in an overflow block 
- In either case, pointer chain must be updated

Need to reorganize the file from time to time to restore sequential order

All of these operations are very costly (??)

#### Ordered File
Records are sorted by ordering field (called ordering key if the ordering field is a key field) 
Advantages: 
- Efficiently read records in the order of the ordering key value 
- Efficiently find the next record 
- Binary search 

Disadvantages: 
- Linear-time insertion in keeping the order. 
- Searching in $\log_{2}$(the number of blocks in the file) 
- I/Os can still be too expensive. 
- Can only support binary search on the ordering field. 

Solution: Indexes


### Single level indexes
Three main types of indexes:
- Primary index (key, ordered) 
- Clustering index (non-key ordered) 
- Secondary index (unordered)

Index Entry `<key, block pointer>`: represent the blocks in a file using a set of pairs - each pair is called an index entry

Indexes are used to speed up record retrieval in response to certain search conditions. Any field can be used to create an index. Multiple indexes can be constructed. 

The index on the ordering field of the ordered file 
- Primary index: the index on the ordering field which has a “unique” constraint.
- Clustering index: the index on the ordering field; multiple records can have the same value. 

Secondary index: on attributes other than the ordering field 
- Index stores each value of the index field (attribute) with a list of pointers to all disk blocks that contain records with that field value. 
- Values in the index are ordered.

#### Density
Indexes may be dense or sparse:
**Dense index** has an index entry for each search key value in the data file.

![[Pasted image 20240503203950.png | center |340]]

![[Pasted image 20240503204007.png | center | 350]]

**Sparse index** has entries for only some search values, e.g., one index entry in the index file for each block in the data file

Applicable when records are sequentially ordered on search-key
To locate a record with search-key value K:
- Find the index record with the largest search-key value < $K$ 
- Search file sequentially starting at the record to which the index record points
#### Primary Index
In a block, the record whose key goes to the index entry is called the anchor record or block anchor

![[Pasted image 20240503203525.png | center | 400]]
#### Clustering Index
Clustering field: file records are physically ordered on a non-key field without a distinct value for each record. 

The clustering index is a file on index entries `<field value, block pointer>`
![[Pasted image 20240503204200.png | center | 400]]

#### Secondary Indices
- A file of index entries. 
- Improve search time for the indexing field.
- A secondary index must be dense.
- More access time than the primary index for “range queries”.
![[Pasted image 20240503204454.png | center | 400]]
#### Summary
![[Pasted image 20240503210519.png | center | 400]]

### Multi Level Index
Single level indexes have multiple drawbacks: 
- An index file is a file, scanning a file can still be costly. 
- To keep the index file in the order of the index field, the update cost is linear.
- If an index does not fit in memory, access becomes expensive

Some terminology and example specific values
Fanout: The maximum number of children an index block can have, here ($f=4$)
The number of tuples in the file $n=26$
The number of blocks in the file $\frac{n}{2}=13$
The cost of finding a tuple with a specific key: $\lceil \,\log_{f} \frac{n}{2} \rceil + 1 = 3$ I/Os

Range Query: 
- Finding all tuples with keys in range \[a, b\]. 
- Find the first tuple with key $\geq a$, and then sequential scan the data file.

Conventional multi level trees are great at searching, but are not as good for inserting/deleting. 

### B+ Tree
There are many types of "B Trees" but B+ is the most efficient and used by many DBMSs. Uses a tree structure to store indexes efficiently. 

The name B-Tree is usually used to represent a family of self-balanced tree structures that keeps data sorted and allows search, sequential access, insertions and deletions in $O(\log _{f}n)$ I/Os. Here $n$ refers to the fanout which is usually significantly larger than 2.

B+-Tree is the most popular data structure in this family, in which the data entry/data pointers are stored only at the leaf nodes
![[Pasted image 20240503214022.png | center | 500]]

By only storing pointers in leaf nodes, fanout is greatly increased. 

![[Pasted image 20240503214135.png | center | 500]]

B+-tree with two parameters 
Blocking factor of the index files (index fanout) $f_{i}$ and 
Blocking factor of the data files $f_{l}$

Each leaf node contains at most $f_{l}$ data records and at least $\large \lceil \frac{f_{i}}{2} \rceil$ data records

Each internal node (except the root) contains:
- $f$ tree pointers and $f-1$ keys
- $f$ is at most $f_{i}$ children
- f is at least $\large \lceil \frac{f_{i}}{2} \rceil$

![[Pasted image 20240507095452.png | center | 500]]
#### Dynamic Tree
A B+-tree is a multi-way search tree with the following properties:
- An **insertion** on a full node incurs a **split** (i.e., overflow)
- A **deletion** from a half-full node causes a **redistribution**/**merge** over the current node and its  neighbours (i.e., underflow)
- The split and merge operations can be triggered recursively.

B+-Tree is I/O aware, and supports equality searches and range queries:
- Make 1 node = 1 physical page  
- Balanced height-adjusted tree
- Makes leaves into linked list (for range queries)

#### Equity Search
Searching for 6: Start at root, is = so go right, then go left, arrived at leaf node. 

For range from 6 to 15, find 6 then go right getting leaf nodes till find >= 15
![[Pasted image 20240507095225.png | center | 400]]

#### Insertion
1. Start from the root, find the leaf where the tuple should be inserted based on the key.
2. Deal with possible overflow:
    - By inserting (2, 0) the leaf node overflows, it has three tuples.
    - Evenly divide the tuples of the overflow node into two nodes. (Note: if the # of tuples is odd, make the first node larger.)
    - Find the key 𝑲 of the first tuple of the second node.
    - Register 𝑲 to the parent node as a key, in ascending order
![[Pasted image 20240507095551.png | Centrer | 300]]
![[Pasted image 20240507095801.png | center | 400]]

Example, $fi=3, fl=2$
![[Pasted image 20240507100614.png | center | 400]]

#### Deletion
1. Start at the root, find the leaf 𝐿 where the entry belongs.
2. Remove the entry.
3. If 𝐿 is at least half-full, done; otherwise, process the underflow :

• Try to re-distribute, borrowing from sibling(adjacent node under the same parent of 𝐿)

- If redistribution fails, merge 𝐿 and its sibling and then delete the corresponding key & pointers in the parent.


Visualise a simple B+-Tree: https://www.cs.usfca.edu/~galles/visualization/BPlusTree.html

#### Complexity
![[Pasted image 20240507101223.png | center | 500]]
![[Pasted image 20240507101241.png | center | 500]]

#### Batch Construction
Batched Construction of B+-Tree

- External Memory Sort the tuples in the file. 
- Construct internal nodes level by level.
• Cheaper than repeatedly inserting tuples by a factor of $O(f_{l})$.


### Buffered Tree
- Concurrent updates on a B+-tree 
- Write-intensive workload  , lots of insertion and deletion.
- Buffered tree

Only the buffer for root is stored in memory, when root buffer gets full purge main buffer and push IO instructions down to buffers in disk for other nodes. 
![[Pasted image 20240507101722.png | Center | 400]]

You can use this same concept to augment any tree structure

### Hashing Index
Better index for equality search. 

Hashing is used lots in `nosql` databases but is not has common in relational DBs

- Representing data in (key, value) pairs
- A has table is an unordered associative array that maps keys to values. 
- A hash function is used to compute an offset in to the array for a given from which the desired values can be found. 

![[Pasted image 20240507102228.png | center |300]]
    
SpaceComplexity O(𝑁):
- 𝑁 denotes the # of tuples of the relation
  
Operation complexity (insertion/deletion/search): 
- Average:𝐎(𝟏)
- Worst:𝐎(𝑵)  
### Hashing with Chaining
For a has table of records size $p=13$
![[Pasted image 20240509172415.png | center | 500]]

Collision is where two or more keys are mapped to the same hash value
Adding `226` whose hash value $h(226) = 226 \text{ mod } 13 = 5$ leads to a collision

![[Pasted image 20240509172515.png | center | 400]]

Bucket overflow can occur because of 
- Insufficient buckets 
- Skew in distribution of records. This can occur due to two reasons: 
	- Multiple records have same search-key value 
	- Chosen hash function produces non-uniform distribution of key values 

Although the probability of bucket overflow can be reduced, it cannot be eliminated.  There are ways of handling overflows. 

### Drawbacks of Static Hashing
Search Process Cost : The maximum number of blocks in one bucket. What may happen when the databases grow or shrink with time?

![[Pasted image 20240509172757.png | Center | 400]]
When the databases grow or shrink, the performance of static hashing deteriorates. 

Solution: 
- Periodic rebuilding the hash file with new hash functions 
	- Rebuild the hash file every day, choose M according to the size of the databases 
	- Rebuild the hash file whenever the database size grows (shrinks) by a factor of 2 
	- Expensive and disruptive: all data records will be copied in the rebuilding 

- Extendible hashing: the performance of the file does not degrade as the file grows

#### Internal to External memory
![[Pasted image 20240509173208.png | center | 500]]

Search (Brandt, Comp. Sci.): 
- Get the first $d=3$ digits of $h(\text{Comp Sci}): 111$ 
- Get the bucket of the directory 111 
- Traverse the bucket and its chained buckets

![[Pasted image 20240509173451.png | center | 300]]

*Why we setup a parameter of global depth?* 
The global depth is used to control the size of directory based on the size of the data, more data leads to more collision (keys mapped to the same bucket) and an overflow of a bucket may lead to an expansion of the directory which will increase the global depth, then the overflown bucket can split. 

*What is a local depth marked in the previous diagram?* 
While some buckets have overflown and split, the other buckets are quite healthy and do not need to split. These buckets stays unchanged and are mapped by multiple cells in the dictionary all binary codes with the same “local depth-prefix with the bucket will be aggregated in the bucket. 

Local Depth of the $j$-th bucket is denoted by $d^{\prime}_{j}$. Global depth is $d$

Number of directory items pointing to buckets is $2^{g-l}$ global - local. 

- The global depth $d$ is used to control the size of the directory by the data. 
- The directory is maintained in the main memory to map the prefix of d bits to the disk addresses of buckets. The size of the directory is $2^d$ 
- Multiple cells of the directory sharing the same prefix may point to the same bucket, this micro-data- saving schema is controlled by “local depth”. 
- A bucket is normally a block/page; when the hashing of all the tuples in an overflown bucket are the same, chaining will be applied.

### KD-Tree (Spatial Databases)
Databases can store data types such as lines, polygons, in addition to raster images 
- Allows relational databases to store and retrieve spatial information 
- Queries can use spatial conditions (e.g. contains or overlaps). 
- Queries can mix spatial and nonspatial conditions

#### Spatial Data
- Nearest neighbour queries, given a point or an object, find the nearest object that satisfies given conditions. 
- Range queries deal with spatial regions. e.g., ask for objects that lie partially or fully inside a specified region. 
- Queries that compute intersections or unions of regions. 
- Spatial join of two spatial relations with the location playing the role of join attribute

#### Indexing of spatial data
A k-d tree is an early structure used for indexing in multiple dimensions. Each level of a k-d tree partitions the space into two. 

- Choose one dimension for partitioning at the root level of the tree. 
- Choose another dimensions for partitioning in nodes at the next level and so on, cycling through the dimensions. 

In each node, approximately half of the points stored in the sub-tree fall on one side and half on the other.  Partitioning stops when a node has less than a given number of points.


![[Pasted image 20240509175327.png | center | 300]]
##### Quadtree
Each node of a quadtree is associated with a rectangular region of space; the top node is associated with the entire target space. 

Each non-leaf node divides its region into four equal- sized quadrants 
Correspondingly each such node has four child nodes corresponding to the four quadrants and so on 
Leaf nodes have between zero and some fixed maximum number of points (set to 1 in the example).

![[Pasted image 20240509193546.png | center]]