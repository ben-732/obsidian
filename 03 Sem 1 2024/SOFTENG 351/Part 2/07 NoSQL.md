Not only SQL databases.  Refer to databases other than traditional RDBMS. 

Contains a range of features, like:
- A simple data model (Relational);
- A declarative query language (SQL);
- Transactional guarantees (ACID).

It has been recognised over the past decades that RDBMS may not be the best solution for many problems
- Semi-structured data storage (graphs, key value pairs, etc)
- High performance
- Availability
- Data replication
- Scalability

#### Example: Wikipedia
Data:
- A large collection of documents combining both structured and unstructured data.

Task:  
- Retrieve all the introduction paragraphs of European countries.  
-  Retrieve all pages with "New Zealand" appeared in the introduction.
### NoSQL Systems
- Key-value stores  
- Document stores  
- Column-based database 
- Graph database systems

Examples.
- BigTable: Google’s proprietary NoSQL system, column-based or wide column store
- DynamoDB (Amazon): Key-value data store
- Neo4J and GraphBase: Graph-based NoSQL systems

### Characteristics
Related to data models and query languages 
- Schema not required  
- Less powerful query languages  
- Versioning

### Key-Value Stores
Key-value stores focus on the high performance, availability, and scalability of structured, unstructured, or semi-structured data.

- **Key**: Unique identifier associated with a data item, used for fast retrieval.
- **Value**: The data item itself, can be a string or array of bytes; the application interprets the value.

Examples:
- **DynamoDB**: Part of Amazon’s Web Services/SDK platforms (Source)
- **Google Bigtable**: Introduced the terms SSTable and MemTable.
- **Apache Cassandra**: Offers features from several NoSQL categories, used by Facebook and others.

### Log Structured Storage
Search build hash table in main memory. Constraint keys must fit in the main memory.
- Update append-only log
- Strength fast read/write 
- High-performance reads/writes, subject to the requirement that all the keys fit in the memory.

![[Pasted image 20240531081553.png | center | 500]]

The log file may run out of space. How to resolve this?

Break the log into segments of a certain size by
- Closing a seg file when it reaches a certain size
- Making the subsequent write to a new seg file.

Compact the segments: remove duplicated keys and keep the latest value of each key.

![[Pasted image 20240531081734.png | center | 500]]

Multiple threads can be compacted and merged in one pass. Done using a background thread and saved to a new file. 

#### In Practice
Deleting Records:
- Tombstone records. (Setting key as empty)

Crash Recovery:
- Reconstruct the hashmap from scratch or use hashmap snapshots of each segment.

Concurrency Control:
- Single write and concurrent read.

Limitations:
- Keys must fit in the main memory.
- Poor support for range search (e.g., scan over all keys between `kitty00000` and `kitty99999`).

Solution:
- **Sorted String Table (SSTable)**: Enforce each segment to store the key-value pairs in the order of the key.
- **Log-Structured Merge Tree (LSM-Tree)**: Maintain tree-based sparse index (on keys) on the segments for searching.
![[Pasted image 20240531082503.png | Center | 500]]

#### SSTable
![[Pasted image 20240531082534.png | center | 400]]

#### LSM Tree (Size Tiered)
Maintain SSTables with a Log-Structured Merge-Tree (LSM-Tree)
In Main memory, keep a balanced tree to index newly arrived key-value pairs

- When the memory is full, export the pairs to the disk as the 𝑖𝑡h SSTable file, and then increase 𝑖 by 1 (𝑖 was 0 initially).
- Newer and smaller SSTables are successively merged into older and larger SSTables. 

![[Pasted image 20240531082753.png | Center | 500]]

Query SSTables on a Log-Structured Merge-Tree (LSM-Tree) :
- Main memory, keep a balanced tree to index newly arrived key-value pairs
- Search the balanced tree in the main memory of the key. If fail, visit the indexes of the green files until finding the key

The key range is split up into smaller SSTables and older data is moved into separate “levels.”

![[Pasted image 20240531083348.png | Center | 500]]

#### Bloom Filter
LSM tree can be slow when looking up keys that do not exist in the database

We have to check the `MemTable`, then the segments back to the oldest (possibly having to read from disk for each one) before you can be sure that the key does not exist.
    
To optimise this, storage engines often use additional Bloom filters    

Bloom Filter: Speed up the query when the search key is not in the data set.

![[Pasted image 20240531083511.png | center | 500]]
##### Application - Email Spam Filter

- $|S|$ = 1 billion email addresses
- $|B|$ 8 billion bits

We know 1 billion “good” email addresses. If an email comes from one of these, it is NOT spam

- If the email address is in $S$ then it surely hashes to a bucked that has the bit set to 1, so it always gets through (no false negatives)
- Approximately some bits are set to $1$ so some of the addresses not in $s$ get through (false positive)
 ![[Pasted image 20240531083918.png | center | 400]]
### Transaction Processing and Analytics
OLTP: Online transaction processing  
- Lookup: a small number of records
- Insert/Update: A small number of records that are related to the user 
- ACID

OLAP: Online analytical processing
- Retrieve: massive number of records, a few columns per record.
- Query: Aggregation, statistics for
- Decision-making business intelligence
	- Financial Performance - What was the total revenue of each user in Jan
	- Promotion - How many more bananas than usual did we sell this month
	- Loan - What is the total amount of money spent vs saved by individuals


![[Pasted image 20240531085028.png | Center | 500]]

#### Schemas

**Stars and Snowflakes**
Fact table: records for individual events recording who, when, where, why and how of the event, typically 100+ columns.

A column is either
- An attribute 
- A foreign key to a dimension table

A dimension table is:
- A smaller table on one attribute
- Can also be wide
![[Pasted image 20240531085527.png | center | 400]]

#### Column Oriented Storage
Typical Query: Aggregate over all rows, only relate to a few columns.

For fact table with trillions of rows and hundreds of columns, column store is popular.

Example query: Get data to analyse whether people are more inclined to buy fresh fruit or candy, depending on the day of the week.

![[Pasted image 20240531085703.png | Center | 300]]

- Column file containing the rows in the same order.
- Only loading those columns from disk that are required for a query.
- Further reducing the demands on disk by compressing data.

![[Pasted image 20240531085837.png | center | 400]]

Creating bitmap by effectively rotating data 90 degrees, determine if a value appears in the table. 

A batch of 64 bits can be computed in one operation. 

Bitmap encoding.: 
- `WHERE product_sk IN (30,68,69)`:

Loads the bit maps of:
- `product_sk= 30`
- `product_sk= 68` 
- `product_sk= 69`

Calculate the bitwise OR

![[Pasted image 20240531090207.png | center | 500]]

Further optimization.
- Vectorised processing on Single-Instruction-Multi-Data (SIMD)
- Sort the data in multiple orders for 
- RAID
	- Fast query processing 
	- Concurrency

##### Aggregation
Cross-tabulation (cross-tab, pivot-table).
- Values for one of the dimension attributes form the row headers
- Values for another dimension attribute form the column headers
- Other dimension attributes are listed on top
- Values in individual cells are (aggregates of) the values of the dimension attributes that specify the cell.

These are simulated to materialised views
![[Pasted image 20240531092909.png  | center | 350]]

Data Cube.
- A data cube is a multidimensional generalisation of a cross-tab
- Can have 𝑛 dimensions
- Cross-tabs can be used as views on a data cube
![[Pasted image 20240531093053.png | center | 300]]
3 dimension data cube.

### Document Database
For a mostly self-contained document such as a resume, a JSON presentation can be quite appropriate.

![[Pasted image 20240531093315.png | center | 400]]

A document is a record and typically contains information about a single object along with its associated metadata.

Documents store data using field-value pairs. They are often stored in formats like JSON, BSON, or XML.

![[Pasted image 20240531093428.png | center | 500]]

Common databases include
- MongoDB 
- RethinkDB 
- CouchDB 
- Espresso


#### JSON Documents
A type of document that reflects “one-to-many” relationships.
- Better locality than multi-table schema. 
- Schema flexible.

A document consists of: elements, attributes, and texts.
- Has a single root element that encompasses all the other elements.
- Properly nests elements.

XML: Another important document type, more verbose than JSON.

### Query Language for a Document
A well-formed document can be parsed into an object under Document Object Model (**DOM**) and then can be traversed/queried upon.

`//user[.//job/@job_title=’Co-chair’]/first_name`
- XPath: an expression on an XML document that returns a sequence of items (values, elements or attributes) that satisfy a certain pattern.
- Starting from the root, an XPath steer the search on the tree of DOM with
    - // search within decedents one-by-one
    - / search within children one-by-one
    - specify the selection condition
    - @ among the attribute of the current node
    - . the current node
- XQuery: SQL-like query language for querying both XML and JSON data
- FLOWR: For, Let, Order by, Where, Return  
    `for $x in docname//user`  
    `where $x//job/job_title=’Co-chair’`
    `return $x/name`

### Graph Database
Good for many to many relations,

A set of vertexes and a set of edges that connects vertexes.
- Knowledge graph 
- Social networks  
- Webgraphs  
- Roadnetworks

Queries
- Traverse  
- Path search  
- Pattern matching

![[Pasted image 20240531094155.png | center | 500]]

#### Example
Cypher. A declarative query language for property graphs. Created originally for Neo4j. A statement consists of one or more clauses. A clause consists of statements and expressions.

**Vertex**
- A unique identifier  
- A set of outgoing edges  
- A set of incoming edges  
- A collection of properties (key-value pairs)

**Edge**
- A unique identifier
- The head and tail vertexes
- A label to describe the type of the relation
- A collection of properties (key-value pairs)
![[Pasted image 20240531094509.png | Center | 400]]

CREATE
```cypher
(NAmerica:Location name:’North America’, type:’continent’), 
(USA:Location name:’United States’, type:’country’ ), 
(Idaho:Location name:’Idaho’, type:’state’ ),  
(Lucy:Person name:’Lucy’ ) 

(Idaho) -[:WITHIN]-> (USA) -[:WITHIN]-> (NAmerica),											 (Lucy) -[:BORN_IN]-> (Idaho)
```



### Graphs in RDB
![[Pasted image 20240531094606.png | center | 400]]
