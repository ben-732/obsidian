>[!readings]
>- Textbook 14.7 Recovery 
>- You may also want to read about ZFS on Wikipedia.



### Recovery From failure
Because a file system has permanence even when something goes wrong, we need to maintain consistency of file data and metadata if possible. 

It needs to ensure that system failure does not result in loss of data or inconsistencies

What can go wrong?
- Metadata can be inconsistent with actual data
	- Location information, size of file, protection rights, times etc
- Free pointer blocks
- FCB (Inode or MFT Entries) pointers lost

#### Example
Deleting a unix file
1. Remove file name from directory
2. Free disk blocks used by the file
3. Remove inode information
4. Add inode back to available nodes

If something goes wrong between 1 and 2:
A file is stored without and place in the directory, effectively wasting disk space

3 and 4:
Space in the inode table is wasted. 

#### Recovery techniques
**Consistency checking** compares data in directory structure with data blocks on the disk. If it finds any inconsistencies it tries to fix them. This is `fsck` on Unix and `chksdk` on windows. It is very slow and might not always succeed. 

**Backup and Restore** Periodic backups to external devices or cloud storage so data can be restored from a known good state

**Journaling** A system that keeps track of changes that will be made in the file system to ensure integrity
- For data integrity during normal operation

**Versioning and snapshots** Maintaining historical version or states of files and systems to roll back to when needed. 

### Journaling File Systems
This is used by many modern file systems to ensure data consistency and prove recovery in the event of failures. (NFTS, ZFS, Many Unix systems)

Keeps track of changes and tries really hard to make sure we don't lose this information

Usually works on file system metadata 

Maintains a log (journal):
- Add log entry for transaction (On disk)
- Can return to the calling process when committed to the log
- Carries out the transaction (Asynchronously)
- Checks transaction (Marks as complete or removes from log)

If something goes wrong at any point it can redo actions of incomplete transactions. 

#### Physical / Logical Journaling
There are two types of journals

**Physical Journal**
All changed data is recorded in the journal first, then written to the file system. This means that the write happens twice which is slower.

**Logical Journal** Only changed metadata like inode tables/directory structures are recorded. This is faster but ==actual data may not be consistent with metadata==. 

### Versioning file systems
Can be useful to retain and access older versions of files. 
- We can recover from mistakes
- We can restore damaged files
- We can compare versions to see the changes made (track changes)
- Useful for security purposes (self-securing storage). 
- Useful for collaboration (say when multiple people work on a file, it's easier to see who made what changes and when)

It is similar to git but works at a lower level, can be done in a variety of ways but all require extra disk space.

#### Methods of versioning 
A new version could be created when the file is closed after changes or after every write (comprehensive versioning).

There are three methods that can be used to create these versions, we can either:
Keep **Complete** copies of all previous versions:
- Very space intensive
- Quick retrieval and no dependencies on previous versions

Keep a **Log of changes**
- Storage-efficient as only differences between versions are recorded 
- Retrieving requires work to reproduce earlier versions

Keep a **tree with all data** 
- Finding any version takes the same amount of time 
- Can be slow for current version if the tree is big

##### Log Versioning 
![[Pasted image 20240504200205.png | center | 400]]

The most recent/current version of the data is always stored in its entirety. Any prior versions are reconstructed by applying the log entries in reverse. 

If we want to be able to roll forward from a checkpoint, we need the new data in lines like `13i13`.

##### Multi version B-Tree
![[Pasted image 20240504200514.png | Center | 500]]
The version ranges (2-?) show which version the leaf is valid for. ? means up to the present.

##### Comparisons
**Log system** 
- compact storage 
- access to the current version is the same as without versioning 
- slow to revert to previous versions especially if there are many versions 
- can use checkpoints to improve this, but this adds considerably to the space requirement 

**Tree system**
- compact storage 
- quick to revert to any previous version 
- If there are lots of versions the tree can be big and then access to the current version will be a little slow 
- Can keep a separate copy of the current version, this also adds to the space requirement 

No method works well if the data between versions is completely different.

#### ZFS Snapshots
ZFS uses a copy on write mechanism that ensures that existing data is never overwritten. Data is written toa new location and only after a successful write does the system update pointers to the new data. 

![[Pasted image 20240504200925.png | Center | 400]]

>ZFS snapshot is a read-only copy of the state of a file system at a particular point in time.

#### Pruning 
All conventional versioning systems use pruning to keep the amount of data stored under control. 

Pruning - selectively remove versions to conserve space or simplify version management. 

There are different heuristics used 
- fixed number of versions 
- treat some changes as more important than others 
- “observe” user behaviour, e.g., most often accessed 
- The user has to explicitly request a version be held 

Snapshot systems keep versions of files at particular times, only keep versions for a small number of files


### Self Securing Storage
All metadata, directories and critical files (OS files) are kept on a versioning system (as a security measure). 

Any intrusion (that uses files) can be tracked because the intruder cannot erase changes they have made to the system. We need to maintain all versions between checks for intrusion. This is referred to as the detection window.

If the system is unable to keep enough versions, we signal an alarm. Either something has gone wrong, or someone is trying to force a pruning to hide their tracks.

## Distributed File Systems
A Distributed file system (DFS) is a file system whose clients, servers, and storage devices are dispersed among the machines of a distributed system 

It should appear to its clients as a conventional, centralized file system.

Key distinguishing feature is management of dispersed storage devices

A client interface for a file service is formed by a set of primitive file operations (create, delete, read, write). It should be transparent; i.e., cannot distinguish between local and remote files

![[Pasted image 20240504202053.png | center | 300]]

It provides many advantages for users:
- Greater storage/scalability, storage of multiple machines can be pooled
- Greater Flexibility, users can log on to any machine and have access to all their files. 
- Reliability
	- If a site goes does, the file may still be accessible from another location (fault tolerant)
	- Data can be replicated across multiple nodes
	- Load balancing -- moving jobs to more lightly loaded sites. 
	- Workloads can be distributed to lead to quicker response times in user facing applications

### Models
There are two widely-used architectural models, client-server model and cluster-based model

Challenges include: 
- Naming and transparency 
- Remote file access 
- Caching and cache consistency

#### Client server model
![[Pasted image 20240504203156.png | center ]]

Server(s) store both files and metadata on attached storage
- Clients contact the server to request files 
- Sever responsible for authentication, checking file permissions, and delivering the file
- Changes client makes to file must be propagated back to the server 

Design suffers from single point of failure if server crashes 
Server presents a bottleneck for all requests of data and metadata 
Could pose problems with scalability and bandwidth

#### Cluster Based DFS Model
![[Pasted image 20240504203321.png | center | 400]]
Files are broken into chunks and stored/replicated across multiple data servers. 

Built to be more fault-tolerant and scalable than client-server DFS

Clients connected to master metadata server and several data servers that hold “chunks” (portions) of files 

Metadata server keeps mapping of which data servers hold chunks of which files as well as hierarchical mapping of directories and files.

File chunks replicated n times

### Naming/Transparency
**Naming** is the mapping between logical and physical objects. 
Abstraction of a file that hides the details of how and where on the disk that file is stored. 
A transparent DFS hides the location where in the network the file is stored. 

For files being replicated in several sites, the mapping should supply one replica while the existence of multiple copies and there locations should be hidden. 

### Naming Structures
**Location transparency** File name does not reveal the file’s physical storage location 

**Location independence** File name does not need to be changed when the file’s physical storage location changes 
- A file name should denote the file's most significant attributes, which are its contents rather than its location. 
- Helps balance the utilization of storage across the system.

In practice most DFSs use *static, location-transparent mapping* for user-level names, Some support file migration, providing location independence (e.g., OpenAFS)

Amazon S3 cloud storage facility provides blocks of storage on demand via APIs, placing storage dynamically and moving data as necessary

### Naming Schemes
There are three main approaches

Files are named by a combination of their host and local names.
- Guarantees a unique system wide name
- Neither location transparent nor location independent. 
- Like an Internet URL (`host:local-name`)

Attach remote directories to local directories
- Gives the appearance of a coherent directory tree. E.g., NFS 
- Complex and most difficult-to-maintain 
- Any remote directory can be attached anywhere on the local directory tree, the resulting hierarchy can be highly unstructured.
- `/shared/data` could be mounted to `/mnt/data` on a local machine

Single global name structures spans all files in the system
- Allowings a similar user experience across different client machines. 
- Open AFS `/afs/example.com/projects/report.txt`
- Used by Auckland `/afs/ec.auckland.ac.nz/b/m/bmcm918`

### Remote File Access
When a user requests to access a remote file, after the server storing the requested file has been located the data transfer must take place

**Remove service mechanism** is one approach to transferring files
- A request for access is delivered to the server
- The server preforms accesses and their results are forwarded back to user. 
- One of the most common ways of implementing remote service is the RPC paradigm
- Analogous to performing a disk access for each access request
- Reduces the need for large data transfers across the network
- Beneficial when only  a small section of a file is required. 

When there is frequent communication, this can lead to heavy network traffic. 

### Caching
Network traffic can be reduced by retaining recently accessed disk blocks in a cache. This means repeated accesses to the same information can be handled locally.
- If data is needed and not cached (cache miss), a copy of the data is brought from the server to the user. 
- Accesses are performed on the cached copy. 
- Files identified with one master copy residing at the server machine, but copies of (parts of) the file are scattered in different caches 
- `OpenAFS` caches files in large chunks (64 KB) 
- Provides faster response times for the user and reduced network traffic

This however raises the issue of keeping the cache copies consistent with the master file
#### Cache Locations 
Cached data can either be stored in main memory or on disk. 

Advantages of disk caches:
- More reliable 
- Cached data kept on disk are still there during recovery and don’t need to be fetched again

Advantages of main-memory caches 
- Permit workstations to be diskless
- Data can be accessed more quickly 
- Performance speedup in bigger memories 
- Server caches (used to speed up disk I/O) are in main memory regardless of where user caches are located; using main-memory caches on the user machine permits a single caching mechanism for servers and user

#### Cache Update Policy
**Write-through** – write data through to disk as soon as they are placed on any cache 
- Reliable, but poor performance 

**Delayed-write** (write-back caching) – modifications are written to the cache and then written through to the server later 
- Write accesses complete quickly; some data may be overwritten before they are written back, and so only last update needs to be written at all 
- Poor reliability; unwritten data will be lost whenever a user machine crashes 

**Delayed-write Variation** – scan cache at regular intervals and flush blocks that have been modified since the last scan (e.g., NFS, but any metadata changes are issued synchronously to the server). 

**write-on-close Variation** , writes data back to the server when the file is closed (e.g., `OpenAFS`) 
- Best for files that are open for long periods and frequently modified

#### Consistency
Is the locally cached copy of data consistent with the master copy:

**Client initiated approach**
- Client initiates a validity check 
- Server checks whether the local data are consistent with the master copy 
**Server-initiated approach** 
- Server records, for each client, the files (or parts of files) it caches 
- When server detects a potential inconsistency, it must react

### Remote service - Stateful
The server knows:
- Who has the file open 
	- For what type of access 
	- Where it is in the file etc. 

When the client calls open, it receives an identifier to be used to access the file. 
- Looks very similar to traditional local file access to the client process. 
- Efficient, the needed data may be read ahead by the server. 
- Information about the file is held in memory. 

If the server crashes it is difficult to start again since all the state information is lost. 

Server has problems with processes which die, needs to occasionally check.

### Remote service - Stateless
Server does not maintain information on the state of the system. It only responds to requests.

Open and close calls don’t send messages to the server. Handled locally. (Except for access privileges.)

Requesting processor has to pass all the extra information with each read/write 
- e.g., the current file location the process is reading from, 
- accessibility information

Server doesn't have to worry about processes stopping 
- it doesn't keep any records and is not taking up any memory space on the server. 

No complicated recovery process if the server goes down. A new server (or the recovered old one) just starts handling requests again.

### AFS
There are two name spaces, local and shared. Local namespace contains files that only appear on the local machine, these are usually machine specific files. 

Shared name space - location information in volume location database (VLDB)

`/afs` is the root of all shared files. Identical on each client and location transparent even over a WAN (SSI –single systems image). 


Files can be relocated without removing access (except for a brief time) 
- Copy them across. 
- Update the location servers. 
- The original location still handles old requests - shipping them to the new location.
- Remove the originals.

Scales easily 

Uses Kerberos for authentication. 
- Login and be authenticated once for all network access. 
- Mutual authentication - clients and servers authenticate themselves.

#### Implementation
The Volume Location Database (VLDB) contains the location information and is usually held by several servers. Servers are dedicated; they are not also clients.

Files are grouped into volumes.
- A volume is commonly the files of a particular user or groups of users.
- The volumes are the things that are referenced in the location database. 
- Volumes can be transparently migrated.

Files identified by `volume:vnode_number:uniquifier` 
- `vnode` numbers can be reused, therefore to keep uniqueness there is the "uniquifier" (extra bits added on until unique)

Client machines run a Cache Manager process •
- Finds where files are (from the VLDB). 
- Retrieves files from the host. 
- Uses caching rather than remote service 
- Files are cached in large chunks (64K) - hopefully the whole file 
- This minimises network traffic and is usually more efficient at both client and server ends

Callbacks 
- A call-back in AFS is a promise from the server that the cached version of a file is up to date.
- Before a file is changed the server breaks the call-backs. 
- Then when another process uses its cached version the Cache Manager detects the broken call-back and refreshes its cached data from the server.

AFS is not stateless
![[Pasted image 20240518193741.png | Center | 300]]

### Remote Procedure Calls (RPC)
**Birrell & Nelson 1984**
- Hide the message passing system so that it looks like a series of procedure calls.
- Most requests for service wait until the request is fulfilled – semantically just like a procedure call. 
- Programmer doesn’t have to package and unpackage data in the messages.

![[Pasted image 20240518194318.png | center | 300]]

#### Stubs
- Client makes ordinary procedure call to the local stub. 
- Stub marshals parameters (may need to locate server as well). 
- Stub sends request via local kernel. 
- Remote kernel passes request to Server stub. 
- Server stub unpacks message request and parameters. 
- Makes ordinary procedure call to Server. And then vice-versa. 

The stubs at both ends need to be constructed from the same interface specification (interface definition languages) - to ensure consistency. 

Care has to be taken about different versions of the service. A different version may take slightly different parameters or return different types etc

#### Marshalling
Heterogeneous networks! 
Data formats in the different machines may be different. 
- ASCII, Unicode, EBCDIC for strings 
- Big or little endian for integers 
- Different floating point formats 

Stubs need to know about this, there are different solutions:
- Client stub converts to the server format; server stub converts from the client format •
- Convert to and from a canonical format
	- No one needs to know other machines formats 
	- e.g. NFS’s XDR - external data representation

#### RPC Messages
Messages are highly structured 
- What procedure to execute 
- Parameters 
- Version number (service may survive a long time and code may be written to different versions)
- Timestamp (could be used for synchronization and security purposes) 
- Source address - where to send the results 
- Possibly the type of machine the request comes from 
Finding the server 
- Include port numbers at compile time  
- Have a binder or rendezvous/matchmaker service 
- Server usually registers with a binder or name server 
- Client end sends the request to find the server 
- Multiple servers – the binder can spread the load 
- Binder can periodically check on servers 
- Deregistering those that don't respond 
- Binder could do the security work 
- Otherwise servers have to check each call

#### RPC Programming
![[Pasted image 20240518195248.png]]
