### Physical Storage Media Classification
**Volatile storage:**
- Lose contents when power is switched off 
- This is like ram

**Non-volatile storage:**
- Contents persist even when power is switched off  
- Includes secondary and tertiary storage, as well as batter-backed up main-memory


### Storage Hierarchy
Choice of storage media  
- Speed with which data can be accessed 
- Cost per unit of data  
- Reliability

![[Pasted image 20240425124721.png | center | 300]]

![[Pasted image 20240425124818.png | Center | 500]]
#### Magnetic Disks
- Read-write head
- Surface of platter divided into circular tracks, each track is divided into sectors
- To read/write a sector, the disk arm swings to position the head on the right track
- Platter spins continuously, head reads data it passes over sector
- Cylinder $i$ refers to the $i$-th track of all platters
- Disk controller interfaces between the computer and disk hardware
![[Pasted image 20240425125055.png | Center | 400]]

Performance of disk is measured by:
Access time: a read or write request staring from sector x, track y 
- Seek time: disk arm to the y-th track  
- Rotational latency: align the read/write head to sector x

Transfer delay: rotate the spindle so that the data that stored consecutively on the disk can be transferred

Sequential access is far more efficient than random access as you don't need to wait for the head to move to a different location. 

### Blocks / Pages
>This is referring primarily to magnetic disks?
- The operating system (OS) formats/initialise the tracks of the disk into equal-sized blocks (or pages).
- Block: a logical unit consisting of a fixed number of contiguous sectors.
- Block size is fixed during initialisation and cannot be changed dynamically.
- Input/Output(I/O): The OS transfers data between the main memory and the disk in blocks, one transfer of a block is called an I/O
- The block size ranges from 512 bytes to 8KB.
- The number of I/O is used to evaluate the efficiency of a data structure or algorithm
- Database sometimes calls a block a page

A small block requires more IO operations but is more space efficient than a large block.
### File Storage
- The DBMS stores a database as a collection of files on disk.  
- A file is a collection of blocks (pages).  
- A block (page) can contain tuples of meta-data, indexes, and log records.  
- Each block (page) is given a unique identifier.  
- Organises blocks of a file to support create/get/write/delete/traverse operators:

![[Pasted image 20240425130138.png | center | 500]]

#### Tuples in a page
Deleting a row creates a gap in the database, the compact command in postgres will allow you to shift the data to remove the gap
![[Pasted image 20240425130243.png | center | 500]]
![[Pasted image 20240425130357.png | center | 500]]
#### Variable-Length Tuples
Slotted Pages
1. The Header keeps the # of used slots and the offset of the smallest offset of a used slot.
2. The slot array maps the slots to the tuples’ starting position offsets.
 ![[Pasted image 20240425131725.png | center | 300]]
#### Page IDs
Each page has a unique Page ID — the page directory layer will map the Page ID to physical address.

Each tuple has a unique Tuple ID: Page ID + offset/slot.
    
These IDs can only be used for checking errors, no application can rely on them.
  
  Example:
- PostgreSQL: CTID(6-bytes) 
- SQLite: ROWID (8-bytes)  
- ORACLE: ROWID (10-bytes)
#### Fields in tuples
A tuple is essentially a sequence of bytes, it is the DBMS that interprets the bytes into attribute types and values.

• `INTEGER`/`BIGINT`/`SMALLINT`/`TINYINT`: 2 or 4 bytes  
• `FLOAT`/`REAL` : 4-10 bytes  
• `NUMERIC`/`DECIMAL` : fixed-point decimals, variable size  
• `VARCHAR`/`VARBINARY`/`TEXT`/`BLOB`: variable length  
• `TIME`/`DATE`/`TIMESTAMP` : 32/64-bit integer of microseconds

## Storage Access
We like the speed of memory but the non volatile properties of disk storage. We need to move data between the two..
![[Pasted image 20240428211739.png | center | 500]]

**Buffer (or buffer pool):** The part of the available main memory used for storing copies of disk blocks.
Aim: to reduce the number of block transfers (I/O) between the disk and memory. 


**Buffer Manager....**
### Buffer Manager
The subsystem which allocates the buffer space and manage the pages in the buffer pool. For each page in the buffer it maintains: 
- A pin count: the number of current users (e.g., transactions or programs) of the page; if the pin-count goes to 0, the page is unpinned 
- A dirty bit: is set to 1 whenever the page is updated by any application program.
The page directory is a non-volatile mapping from Page IDs to Page locations
![[Pasted image 20240428212209.png | center | 200]]


The buffer pool is an array of frames, the size of a frame is the size of a page. 

When the DBMS requests a page, a copy is placed in a frame. The *page table* maps the IDs of the pages that are currently in the buffer pool to the corresponding frames, and maintains the meta-data for each page 
- Dirty flag: a binary state. The page is dirty if the page is updated since loaded from the disk. •
- Pin: an integer, stating the number of threads that is using the page. The page is unpinned if `pin = 0`

>The buffer manager cannot ensure non-volatile storage unless every update would be written back immediately which would be problematic to the update speed. 
#### Reading
Read request: a page ID X. 

Buffer manager, upon receiving a read request 
- Check the page table, if the page is not in the buffer pool 
- If the buffer pool is full, perform *buffer replacement* to get an empty frame load X to the buffer and update the page table. 
- Return the content of the page from the corresponding frame. 

**Buffer replacement:** Choose, among all the unpinned pages, one page Y based on a replacement policy
- If Page Y is dirty, write back Y to the disk, then kick the page out of the buffer pool. 
- Write back: either overwrite the original block or write to a new location and then mark the original block as invalid.

When the DBMS needs to free up a frame to make room for a new page, it must decide which page to evict from the buffer pool.

The goal of the buffer is to enable an illusionary quickly accessible non-volatile massive disk storage, i.e., to increase the “hit rate” the proportion of read requests whose page is in the buffer pool without triggering an I/O. There are multiple different replacement protocols that a DBMS can use 

The hit rate for a replacement strategy is considered to be `= num hits / num requests`
##### Replacement: Least Recently Used (LRU)
Maintain a single timestamp of when each page was last accessed and select the one with the oldest timestamp to be evicted. 

 Heuristic: The page that is used more recently is more likely to be used later. 

This protocol is not often used as it requires storing a timestamp for every single accessed page which comes with a lot of overhead. 

##### Replacement: Clock
This removes the overhead from LRU

Maintain a binary bit for each page, when accessing the page, set the bit to `1`
- Reset bits with value `1`  to `0` using a “sweeping clock hand”
- When the clock hand reaches a page and the bit of the page is `0` the page has not been accessed for the past whole circle, evict the page;  

![[Pasted image 20240428213809.png | center | 300]]

Heuristic: Approximate LRU without keeping a timestamp per page.

##### Replacement: Most recently used (MRU)
This is good in situations with sequential flooding. When a join or a cross product of two relations is preformed, we have to repetitively rescan the second relation. Here we can encounter a behaviour with either Clock or LRU strategy where items are evicted from the buffer pool immediately before they are going to be required again.

MRU Removes the item that was most recently read which leads to a slightly better hit rate

Maintain a single timestamp of when each page was last accessed. Select the one with the youngest timestamp to be evicted.

Heuristic: The page that is used more recently is less likely to be used later

#### External Memory Sorting 
Problem - External Memory Sorting: Strategic Buffering 
- Let $R$ be a relation stored in $n$ pages. Let $m$ be the number of frames of the buffer pool. 
- Assumes that $n \gg m$. Sort the tuples of $R$ while optimizing the number of I/Os incurred.

External Memory Sorting Algorithm 
1. Create Runs (Create $l=\lceil\frac{n}{m}\rceil$ sorted runs) 
2. Merge Runs ($m-1$ way merge)

Cost of I/Os: The algorithm of external memory sorting sorts the tuples in $R$ in $O(n\log_{m}n)$ I/Os.

$m$ is the number of frames available. 

Step 1: Create Runs
Given a relation $R$, repeatedly create runs till reaching the end of $R$. 
1) Read $m$ blocks of $R$ into the buffer pool 
2) Sort the $m$ blocks in memory 
3) Write the sorted $m$ blocks to form the $i$-th ($i$ is 0 initially) run $R^0_{i}$ ". Increment i 

As a result of Stage 1:  $l=\lceil\frac{n}{m}\rceil$ runs are created. 

Note that the last run $R^0_{l}$ may have less than $m$ blocks while the other runs each has $m$ blocks.

![[Pasted image 20240428220012.png | Center | 600]]

Step 2: Combine runs
The notes are kinda bad here,, but sort $m-1$ and use the $m$-th frame to store the output and then save that to the disk. then do the next $m-1$ runs and continue until the number of runs are 1. 

This effectively creates a tree of height $\log_{m-1}\lceil\frac{n}{m}\rceil$ as you originally have $\lceil\frac{n}{m}\rceil$ runs and you reduce them by a factor of $m-1$ each time 

![[Pasted image 20240428221543.png | center]]

![[Pasted image 20240503200225.png]]

##### Maths
If you have $n$ blocks to sort, and $m$ frames of memory to use to sort them. 
You can create and sort in memory $x = \lceil\frac{n}{m}\rceil$ run of length $m$

You can then then do a [[4. Sorting#Merge Sort |merge sort]] combine on the first $m - 1$ frames to create a sorted run of length $m\times (m-1)$, do this for every other set of $m-1$ runs in pass 1 to reduce the number of runs by a factor of $m-1$

This can then be repeated in pass 2 to combine $m-1$ sorted runs of size $m \times (m-1)$ etc etc

It will take $\large y=\log_{m-1}x$ runs to reduce the runs down to one run of length n. 

This will require $\large (y + 1) *2n= ((\log_{m-1}\lceil\frac{n}{m}\rceil)+1)\times  2n = O(n\log _{m}n)$ I/Os to complete.

### RAID 
Raid stands for "Redundant Arrays of Independent Disks", it is a disk organization technique that manage a large number of disks that supports high data capacity, high access speed, and high reliability.

Mean Time To Failure (MTTF) of a single disk: 100,000 hours (approx. 11 years). 

For a system with 100 disks, the MTTF is 1000 hours (approx. 41 days). The chance that some disk out of a set of N disks would fail is much higher than that of a single disk.

To AVOID data loss (reliability), we shall systematically introduce redundancy. As a by product of a wise organization of the disks, we can achieve higher access speed as well.

**Redundancy:** store extra information to rebuild the information lost in a disk failure. 
- Technique 1: Stripping. Improve transfer rate by striping data across multiple disks. 
- Technique 2: Mirroring (or shadowing). Duplicate every disk. Costly. 
- Technique 3: Parity. Improve the reliability by storing error correction bit using xor

Raid has different levels with different levels of redundancy
RAID Level 0: Block striping, non-redundant. 
RAID Level 1: Mirrored disks. 
RAID Level 5: Block-Interleaved Distributed Parity
![[Pasted image 20240503201111.png | center | 500]]
#### Level 0: Stripping
Improve transfer rate by striping data across multiple disks. 
**Bit-level striping:** split the bits of each byte across multiple disks 
- In an array of eight disks, write bit i of each byte to disk i. 

**Block-level striping:** with n disks, block i of a file goes to disk $(i \text{ mod } n) + 1$ 
- Requests for different blocks can run in parallel if the blocks reside on different disks 
- A request for a long sequence of blocks can utilize all disks in parallel

#### Level 1: Mirroring
Redundancy: store extra information that can be used to rebuild information lost in a disk failure
- Duplicate every disk. A logical disk consists of two physical disks. 
- Every write is carried out on both disks 
- If one disk in a pair fails, data still available in the other 

Mean time to data loss depends on the mean time to failure, and mean time to repair

#### Level 5: Parity
Block-Interleaved Distributed Parity: 
- Rather than storing data in $N$ disks and parity in 1 disk,  data and parity is spread among all $N+1$ disks, 

Example: with 5 disks • the parity block for $n$-th set of blocks is stored on disk $(n \text{ mod }5) + 1$ with the data blocks stored on the other 4 disks.