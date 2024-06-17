A transaction is a unit of program execution that accesses and possibly updates various data items

A transaction to transfer $50 from account A to account B
```
1. read(A) 
2. A := A – 50 
3. write(A) 
4. read(B) 
5. B := B + 50 
6. write(B)
```

### Issues

There are two main potential issues to deal with:
1. Failures of various kinds, such as hardware failures and system crashes 
2. Concurrent execution of multiple transactions

#### Recovery Issue
Similar to those covered in [[03 Other File Systems]]

Types of failures: 
- Computer failure (system crash) 
- Concurrency control enforcement 
- Disk failure 
- Physical problems and catastrophes

#### Concurrency Issue
Similar to those covered in [[08 Concurrent Programming]]

Transaction processing systems: systems with large DBs and hundreds of concurrent users 
- **One CPU:** how to serve multiple users concurrently to minimize the average delay?  
	- Interleaved concurrency, e.g., `[t1, t2]`, when a process requires I/O, one keeps the CPU busy by switching to execute another process instead of waiting. 
- **Multiple CPUs:** how to serve multiple users concurrently to minimize the average delay? E.g., `[t3, t4]`.

![[Pasted image 20240518095324.png | center | 400]]

### ACID Instructions
Transactions need to be ACID compliant
![[Pasted image 20240518095516.png | center | 500]]

ACID can be achieved through the following:

```
BEGIN TRANSACTION 
- READ / WRITE OPERATIONS 
	- READ: data retrievals 
	- WRITE: updates, insertions and deletions 
- COMMIT: the transaction completes successfully, all the updates are permanently made to the database 
- ROLLBACK: the transaction ends unsuccessfully, undo the operations in the transaction END TRANSACTION
```

For a data item (named) $X$, a transaction $T$ may carry out two database access operations: 
- `read(X)`: Reads a database item named $X$ into a program variable named $X_{T}$ 
- `write(X)`: Write a program variable named $X_{T}$ to a database item named $X$ 

When the context is clear, we omit the subscription $T$ of a program variable

### Read
- Find the address of the disk block of `X`
- Copy the disk block of `X` to the memory buffer 
- Copy `X` from the buffer to the program variable `X`

### Write
- Find the address of the disk block of `X` 
- Copy the disk block of `X` to the memory buffer 
- Copy `X` from the program variable `X` to the buffer 
- Write the updated block from the buffer back to the disk

#### Atomicity Requirement
Partial updates of read/write are not reflected in DB.

- If the transaction fails after step 3 and before step 6, money will be “lost” leading to an inconsistent database state (From original transfer example)
- The system should ensure that updates of a partially executed transaction are not reflected in the database

#### Consistency
There are four actions that lead to consistency issues

- Lost Update - value is written then overwritten by another process
- Dirty Read - Read in the middle of another process execute. Will cause issues on rollback.
- Unrepeatable Read - Reading committed data items but getting two diff results (other process commits)
- Phantom Read - Reading a record that may or may not be there because race conditions

##### Transaction System Log
A sequential, append only file the tracks transaction operations. 

Each transaction is associated with an ID.
![[Pasted image 20240518101445.png | Center | 300]]

To Ensure database is not affected by failure 
- Log Buffer
- Log file is backed up periodically
- Commit point, under and redo operations
##### Commit Point
A transaction reaches its commit point if: 
- All of its database access operations have been executed successfully, 
- The effect of the transaction operations to the database has been recorded (force-write log buffer before commit point) in the log. 

Beyond the commit point, the transaction is called committed then its effect must be permanently recorded in the database.

#### Isolation Requirement
![[Pasted image 20240518101654.png | center | 500]]

To ensure isolation, you can simply runn transactions serially (i.e., one after the other). However, executing multiple transactions concurrently has significant benefits.

#### Durability Requirement
Persistent update of the transaction if any failures. 
- Once the user has been notified that the transaction has completed • i.e., the transfer of the $50 has taken place 
- The updates to the database by the transaction must persist even if there are software or hardware failures.
C
### Transaction State
![[Pasted image 20240518101809.png | center | 500]]

**Active:** the initial state; the transaction T stays in this state while it is executing 
**Partially committed:** after the final statement has been executed. 
**Failed:** after the discovery that normal execution can no longer proceed. 
**Aborted:** after the transaction T has been rolled back and the database restored to its state prior to the start of the transaction T. Two options after it has been aborted: 
- Restart the transaction, Can be done only if no internal logical error 
- Kill the transaction 
**Committed:** after successful completion.


### Concurrent Expressions
It is beneficial to allow multiple transactions to run concurrently.

1. Increased processor and disk utilization, leading to better transaction throughput 
2. Reduced average response time for transactions: short transactions don't need to wait behind long ones.

![[Pasted image 20240518102156.png | center | 500]]

#### Example Schedules
$T_{1}$ executes full, then $T_{2}$ is executed. There is no concurrency here.

![[Pasted image 20240518102411.png | center | 200]]

The following schedule is not a serial schedule, but it is equivalent to the serial one.  The sum A + B is preserved.

![[Pasted image 20240518102458.png  | center | 200]]
### Serial Schedules
Serial Schedules: place simultaneous transactions in series $<T_{1}, T_{2}>$

This limits concurrency by prohibiting interleaving of operations. 

The solution is to determine which schedules are equivalent to a serial schedule. 

### Serialisable Schedules
Given $n$ transactions $t_{1}$ to $t_{n}$  a serialisable schedule of the $n$ transactions is equivalent to some serial schedule of same $n$ transactions.

#### Conflicting Operations
Two operations are conflicting if changing their order can result in a different outcome. Two operations in a schedule are said to conflict if 
- They belong to different transactions 
- They access the same item X 
- At least one of the operations is write(X)
![[Pasted image 20240518103340.png | center | 400]]

Instructions `li` and `lj` of transactions $T_{i}$ and $T_{j}$ conflict if and only if there exists some item $Q$ accessed by both `li` and `lj`, and at least one of these instructions wrote $Q$.

```
1. li = read(Q), lj = read(Q). li and lj don’t conflict. 
2. li = read(Q), lj = write(Q). They conflict. 
3. li = write(Q), lj = read(Q). They conflict
4. li = write(Q), lj = write(Q). They conflict
```

A conflict between `li` and `lj` forces a (logical) temporal order between them.

#### Conflict Equivalence
Schedule `S` is conflict equivalent with schedule `S'` if the relative order of any two conflicting operations is the same in both schedules

A schedule `S` is conflict serializable if it is conflict equivalent to some serial schedule `S'`

![[Pasted image 20240518103607.png | Center | 400]]
Schedule 1 can be transformed into a serial schedule where $T_{2}$ follows $T_{1}$, by series of swaps of non-conflicting instructions. Thus, Schedule 1 is conflict serializable.

### Testing For Serializability
For some schedule of a set of transactions $T_{1}$ to $T_{n}$ the **Precedence graph** is a directed graph where the vertices are the transactions (names). 

We draw an arc from $T_{i}$ to $T_{j}$ if the two transactions conflict, and $T_{i}$ accessed the data item on which the conflict arose earlier. We may label the arc by the item that was accessed

![[Pasted image 20240518104219.png | center | 300]]
![[Pasted image 20240518104429.png | center | 500]]


A schedule is conflict serializable if and only if its precedence graph is ==acyclic== (no cycle).

Cycle-detection algorithms exist which take order $n^2$ time, where $n$ is the number of vertices in the graph. (Better algorithms take order $n+e$ where $e$ is the number of edges.)

If the precedence graph is acyclic, the serializability order can be obtained by a [[05 Acyclic Graphs + Topological Orders|topological sorting]] of the graph. This is a linear order consistent with the partial order of the graph


### Recoverable Schedules
When a failure occurs on concurrently running transactions, you are sometimes unable to rollback due to the order of read/writes. 

![[Pasted image 20240522202211.png | center]]
> this is a non-recoverable schedule

If $T_{8}$ should abort, $T_{9}$ would have read (and possibly shown to the user) an inconsistent database state. Hence, database must ensure that schedules are recoverable.



#### Cascading Rollbacks
When a single transaction failure leads to a series of transaction rollbacks. 

![[Pasted image 20240522202657.png | center | 300]]

In the following schedule where nothing has been committed yet, if $T_{10}$ fails then $T_{11}$ and $T_{12}$ must also be rolled back, this can lead to the undoing of a significant mount of work. 

#### Cascade-less Schedules
Cascade rollbacks cannot occur; For each pair of transactions $T_{i}$ and $T_j$ such that $T_{j}$ reads a data item previously written by $T_{i}$

The commit operation of $T_{i}$ appears before the read operation of $T_{j}$.

>Every Cascade-less schedule is recoverable


### Isolation Levels
Isolation levels ensure concurrency
```SQL:  
SET TRANSACTION READ ONLY ISOLATION LEVEL READ COMMITTED;  
SET TRANSACTION READ WRITE ISOLATION LEVEL READ UNCOMMITTED;
```
In order from least to most stringent.

![[Pasted image 20240523150636.png | center | 400]]

