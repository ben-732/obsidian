>[!info] Applicable Textbook readings
>- 13.3 Directory and Disk Structure


File systems are different ways of organising and storing files on a storage device. 

We need some way of storing information
- Independently from a running program
- So it can be used at a later time
- Permanently (or an approximation to it)
- Non-volatile storage (power can be turned off)
- So it can be shared with other program (process) or users

There are an infinite variety of ways of data is to be stored
- The more information the OS knows about the data the more it can facilitate use of the data.
- e.g. Documents, Media, Executable files, System files.

Naming the data
- The data needs to be stored and retrieved easily. We need a way to name the data.
- We must then be able to locate the data using its name. (Directory ?)

### What is a file
Textbook definition : A named collection of related information that is recorded on secondary storage. (Not always true, below.)

Secondary storage is usually a disk drive (including SSDs) but it could be a tape drive, or any other non-volatile device.

In some systems “files” are not always files e.g. In UNIX devices are “files” and so are some operating system information structures (e.g. /proc in Linux)

`ls -l /proc`
![[Pasted image 20240416093215.png | center | 350]]

### File attributes
The OS keeps some basic information about  files
- **Name** – The symbolic file name is the only information kept in human readable form.
	- Many systems use a byte to indicate the file name length and so are limited to 255 characters. There are usually limitations on the characters you can use in filenames.
- **Identifier** – unique tag (number) identifies file within file system
-  **Type** – needed for systems that support different types
- **Location** – where is the file stored, some pointer to the device (or server) and the positions on the device
- **Size** - current file size (either in bytes, blocks, number of records etc)
- **Owner information** – usually the owner can do anything to a file
- **Protection** – controls who can do reading, writing, executing
- **Time, date, and user identification** – data for protection, security, and usage monitoring

and so on....
- Information about files (metadata) are kept in the directory structure
- Both the directory structure and the files reside on disk
- Many variations, including extended file attributes such as file checksum

### File Types
The more the system knows about file types the more it can perform appropriate tasks.
 - Include the type as part of file name. (suffix, extension) E.g. test.pdf
-  UNIX also uses a magic number stored at beginning of some files, e.g. PNG

By knowing about a file, the OS can preform different actions based on a given file.
- Executable binaries can be loaded and executed.
- Text files can be indexed.
- Pictures can have thumbnails generated from them.
- Files can automatically be opened by corresponding programs.
- Also the system can stop the user doing something stupid like printing a sound file.

#### Magic numbers
Plain text files do not have a magic number, files like a `.png` do however
```
// Magic number of a png:
89 50 4e 47 0d 0a 1a 0a
```


Hiding a compressed file within a png file:
![[Pasted image 20240416094725.png | center]]

### File Structure
File Type – represents the internal structure of a file.
- .exe - They have an OS specific structure – information for the loader about necessary libraries and where different parts should be loaded and where the first instruction is.
- All operating systems “know” about executable binary files.
- System-supporting multiple file structures
	- Disadvantages – makes OS large and cumbersome (code for all supported structures)
- UNIX – files as a sequence of bytes; no interpretation of these bits is made by the OS.

### File system operations
A file can be considered as an abstract data type that has data and accompanying operations.

On most systems these commands need security authorization to perform and they work on the file as a whole..

#### Create
Need to specify information about the file:

- The name
- The file type (possibly)

We don't need to provide the size of the file, it certainly helps with keeping storage contiguous but is usually regarded as an unnecessary restriction.

Creation needs to do something to the associated device - at least write to some structure (a directory?).

Some systems allow transnsitory files to not be recorded permanently in secondary storage i.e. they only exist in memory

#### Delete
Remove the file. Return the space used by the file. 

Should you Zero the space? On SSDs the space does not need to be copied when the "block" is rewritten.

What if the deletion was a mistake?
- Deletion might keep the file in a “going away” area from which it can be retrieved.
- Versioning file systems maintain multiple versions over time. They can also be used to track down intruders (self-securing storage)
- When data is deleted on an SSD with TRIM (unallocate), it is lost.

![[Pasted image 20240417185746.png | center | 300]]
TRIM clears space by storing content to flash, erasing the cell then rewriting a sorted/optimised version to that cell (or sometimes a new one?).

This shows what happens when a page is modified and then two new pages are written to an SSD block.

#### Open
- Several pieces of data are needed to manage open files:
- Open-file table: tracks open files
- File pointer: pointer to last read/write location, per process that has the file open
- File-open count: counter of number of times a file is open
- Disk location of the file: cache of data access information
- Access rights: per-process access mode information

#### Close
`close()` decreases the open count

When the open count reaches zero, the file is no longer in use - allow removal of data from open-file table when last processes closes it

`create()` and `delete()` are system calls that work with closed rather than open files

#### Move
Moving a file can be performed in different ways depending on the before and after locations.

If both locations are on the same device the data doesn’t have to be copied and then the original deleted. Instead change information about the file (metadata).

#### Copy
Most file systems preserve attributes (including last modified times) when a copy is made.

In some situations we can use *copy on write* rather than making complete copies. The file information needs to point to the original data.

#### Change attributes
Some of these should be changeable by the user, others should be secured by the OS.

#### Read
These operations work on the file contents.

We need to know where the information is on the device. Must specify what data to read, how much, and where to put it.

Sequential access
- Data is retrieved in the same order it is stored
- There is a current position pointer somewhere
- May be stored within the using program
- May be stored within the file system (but separately for each process)
- Has ramifications for distributed systems

![[Pasted image 20240417200404.png | center | 300]]

Direct (or random) access
- Provides immediate access to large amounts of information.
- Easy on a disk device. Even easier on a solid-state device.
- but it depends on how file data blocks are recorded
- The read specifies exactly where it wants to get the data from.
- It could be a byte offset or a record number – is an index relative to the beginning of the file.
- Some file systems let you specify record length when you create a file. Others leave all such control up to individual programs.

#### Write 
Very similar to read but commonly requires the allocation of extra space.

Direct (random) access writes can create holes. The program “seeks” to the new position.

If this is outside the bounds of the file, then we have two choices.
- Allocate all of the intervening space and fill it with some null value
- Mark the intervening space in the directory as not allocated – this is known as a sparse file

If a process tries to read from the empty space of a sparse file, it gets the null value for the record.

If a process writes to the empty space, then real blocks are allocated and written to.

### Design Decisions
Files need to contain vastly different types of information. Some of this information is tightly structured with lines, records etc.

Should the file system allow flexibility in how it deals with differently structured files?
- At the bottom level the file system is working with discrete structures (sectors, pages and blocks) of a definite size.
 
- The most common solution is to treat files as a stream of bytes and any other structure is provided by the programs using the files.

- The work has to be done somewhere. Some operating systems provide more facilities than others for dealing with a variety of file types.


### Representing files on disk
We know that disks deal with constant size blocks (say 4KB).

All disk I/O is performed in units of one block (physical record).

All files and information about files must be stored in these blocks (usually accessible via a logical block number / logical record).

Eg. UNIX treats files to be simple stream of bytes - logical record size is 1 byte.

We need to know what we want our file system to look like to the users in terms of its structure.
We also need to know how we can place the required information on the disk devices

### Directory Structure
Data about files and other information about storage on a device is called metadata.

Usually, a disk device has one or more directories (or tables) to store metadata. These directories can be arranged in different ways.

#### Single-level directory
Simple, small systems did this, especially with small disk devices (floppies)

Disadvantages:
- Finding files as the number of files grows becomes difficult.
- To be workable it requires very long filenames.

The directory entry has a pointer to the location of the actual file. 

#### Two Level Directory
The top level is the master file directory contains files for each user/

![[Pasted image 20240422191820.png | center]]

Creating a user file directory is usually only allowed for administrators. 

When people log in, they are placed within their own directories. Any files mentioned are in that directory. If a user has the appropriate permissions, they are able to access other users' files using the full path name. 

System files can be placed in a single user file that every user has permissions to access. 

#### Tree Structure
Tree structured directories allows as many directories as required. This facilitates the organization of collections of files. 

- Every file in the system has a unique path name, (absolute or relative)
- Directories are special files. 

When deleting a directory, some operating systems will not allow you to continue unless the directory is empty. The unix `rm -rf` command deletes all files and subdirectories (recursively), this is very convenient but also dangerous. 

### Sharing files - Links
We very often want the same data to be accessible from different places across a OS. e.g. two people might be working on a project and they both want the project to be available in their own local directories. 

This can be accomplished in multiple different ways. 

- **Multiple copies:** If the data is read-only, we could just make an extra copy. 
- **Multiple directory entries:** We could make copy of the file record (or directory entry) information (not the data). 
	- There is a problem with consistency and data integrity. 
- **Soft links:** There can be one true file entry in one directory. Other entries have some reference to this entry. 
	- UNIX symbolic links and Windows shortcuts. 
- **Hard links:** There can be a separate table with file information. Then all directory entries for the same file just point to the corresponding information in this table. 
	- UNIX and NTFS hard links

![[Pasted image 20240422192812.png | center | 500]]


> Both UNIX and NFTS can only make links between files on the same volume. 

#### Hard Links
##### On UNIX
In Unix, hard links are created with the `ln existing new` command. 

![[Pasted image 20240422193656.png | center | 300]]

Each directory entry stores a pointer to the file's inode which holds the real information about the file. 

##### On NTFS
- `mklink /h NewFilename ExistingFilename` 
- Each directory entry holds copies of most file attributes plus a pointer to the file’s Master File Table (MFT) file record. 
- NTFS updates the properties of a hard link only when a user accesses the original file by using the hard link. 
- Hard links do not have security descriptors; instead, the security descriptor belongs to the original file to which the hard link points.

#### Soft Links
##### On UNIX Symbolic Links
Created with the `ln` command, the `-s` switch creates a symbolic (soft) link. 


- The file called “f3” is actually just a text file with the contents “f1”.
- The OS knows to treat it differently from other text files because of the “l” in the attributes on the left-hand side. 
- If the original is moved, then UNIX can’t do anything about it. You get unable to open errors for example. 
- Only one actual copy of the content on disk, these links merely contain a reference to another pathname.

![[Pasted image 20240422193656.png | center | 300]]
Here, after deleting f1, f2 the hard link is still accessible but f3 the soft link is not. 

##### On NFTS (Windows) Shortcuts
Using the Context menu in file explorer you can create a shortcut to any file. Windows using attributes like file type, size and time of modification to find renamed shortcuts. 

This is much better under NTFS with the distributed Link Tracking Client service
- It keeps a log of changes made to files that have shortcuts. This means that the file can almost always be found
- It sometimes works on distributed environments. 
##### Mac Aliases
HFS+ (and APFS) has a unique, persistent identifier for each file or folder. 
- An alias stores this identifier with the pathname.
- Originally the identifier was first used to find the file. Only if this failed was the pathname used. Now in order to work more like symbolic links, the pathname is used first. 
- If a file is renamed and a new file with the old name is created, both an alias and a symbolic link will find the same file (the new one). 
- If the pathname does not find the file but the identifier does, then the pathname is updated to the current correct value.

#### Directory Graph Cycles
If we allow directories to appear in multiple places in the file system we can get cycles, We don’t want to fall into infinite loops if we traverse the file system e.g. to search for a file. 

[[05 Acyclic Graphs + Topological Orders|Acyclic-Graph]] Directory - allows directories to have shared subdirectories and files. (there is no way to return to a directory by continuously following directory links within the structure).

For this to work, we need ways of uniquely identifying directories
In UNIX, directories can only be linked with symbolic (soft) links. Algorithms count symbolic links on directories to stop infinite recursion. 

#### Deleting linked files
With hard links, we maintain a count of the number of links. This gets decremented each time one of the links gets deleted. When this finally reaches zero the actual file is deleted. 

With symbolic links if a link is deleted, we do nothing. If the real file is deleted 
- We could have dangling pointers 
- Or we could maintain a list of all linked files and go around and delete all of them.

## Directory Contents
The file name (actually the last part of the pathname), we scan the directory to see if the file exists within it. 

We can have the file attributes stored in the directory entry. (MS-DOS) 
- At a minimum we need a pointer to the file attributes and location information. 
- UNIX keeps the ==file attributes and location information== (pointers to its data blocks on the disk) of each file in a separate structure – the *inode* (Information node or Index node). 
	- The inode also keeps a count of the number of hard links to the file. 
- The inode table is an array of *inodes* stored in one or more places on the disk. 
- So, a UNIX directory isn’t much more than a table of names and corresponding inode numbers

![[Pasted image 20240428222143.png | center | 400 ]]
### NTFS Directory Entries
All file and folder information is stored in the MFT (Master File Table). Each file has at least one file record consisting of the file attributes, size, location etc. 

Folders have an indexed table that lists all the files (and sub-folders) within that folder. Each entry in the folder table includes the file name, a pointer to the file's MFT entry, and some commonly referenced attributes (e.g., created and modified dates, file length). 

So much of the information in the files MFT record is ==duplicated in the directory entry==. This allows the folder table to act as a kind of cache (?)

This explains the hard link behaviour (ability of NTFS to have multiple directory entries (hard links) pointing to the same MFT record) from last lecture. 

An MFT entry for a file or directory:
![[Pasted image 20240428222722.png | Center]]

### Terminology
The things on the disk that hold information about files – 
- The textbook calls them file control blocks (FCB). 

There are several file tables held in different types of memory and accessed by different entities (as we will see). In this case the on-disk representation that holds the file information, attributes and other pointers. 
- In UNIX this is the inode. 
- In NTFS this is the MFT file record. 
- In MS-DOS this is the directory entry.

### Finding File Blocks
There must be a place on the disk which points to the blocks allocated to each file. 

This can be done in many different ways. It depends on how blocks are allocated to files. 


#### Contiguous Allocation
Some early file systems always allocated files in contiguous blocks. So only the start block, and the number of blocks needed to be stored. 
- Pros: Fast access, and simple to implement. 
- Cons: Finding space can be hard, external fragmentation, files can’t grow easily.

Contiguous Allocation Strategies, Have to find a large enough hole. 
- **First fit:** always starts at the beginning of the device and allocate the first hole that’s big enough. 
- **Next fit:** like first fit but next time starts from most recent position 
- **Best fit:** Chooses the smallest hole that is big enough. 
- **Worst fit:** - Allocates the largest hole. 
- **Buddy algorithm:** Splits memory into partitions to try to reduce fragmentation. First/next fit is commonly used.

External fragmentation: lots of holes outside files that are too small to hold bigger files 

How much space should be allocated for each file? 
- If we allocate too little, it is a pain to increase the size
- If we allocate too much, we are wasting space (internal fragmentation)

![[Pasted image 20240428223330.png | center]]

Extents: Collections of contiguous allocation (NTFS) - When expanding file, if not enough blocks available add a pointer to a new contiguous allocation. 

#### Linked Allocation 
Another major method to keep track of the blocks associated with each file is linked allocation - each file is a *linked list of disk blocks*. 

A small amount of space is set aside in each block to point to the next (and sometimes previous as well) block in the file. The directory entry holds a pointer to the first block of the file and probably the last as well for if a pointer is corrupted. 

File Allocation Table (FAT) is a variation of linked allocation where all the links are gathered in one place

Pros:
- Simple, and allows for dynamic growth
- No external Fragmentation

Cons:
- Random Access: Direct (random) access to a particular part of a file can be slow. The system might need to traverse multiple blocks to reach a specific part. 
- Reliability: Damage to one block can cause the loss of a large section of the file (or damage to two blocks if doubly linked). 
	- We could store a file id and relative block number in every block. 
- Space Overhead - A portion of each block is used for the next block's address, reducing block's effective storage space. 
	- If a pointer requires 4B out of a 512B block, then 0.78% of the disk is being used for pointers, rather than for information

#### MS-DOS & OS2 FAT
The MS-DOS File Allocation Table (FAT) is a collection of linked lists maintained separately from the files. One entry for each block on the disk. 

A special section of disk holds the table, separate from the actual data it references. 

Each directory entry holds the starting block number of the file. This number is also an index into the FAT. 

At that index is the block number of the second block. This number is also an index into the FAT. etc…

Over time the number of bits used to index the FAT changed (8 to 12 to 16 to 32) as disks got larger. So, the size of the FAT and the maximum number of allocation units on the disk grew.

![[Pasted image 20240428224659.png | center | 300]]
Pros/Cons
- Accessing the FAT data for a file requires far fewer disk accesses than for normal linked access.
- One block of FAT data might hold information for several blocks in the file. 
- If we have enough memory to cache the entire FAT, we can determine the block numbers for any file with no extra disk access.
- Unfortunately, as the FAT gets larger it is more difficult to cache the whole thing and so it becomes more common for a single block access to require multiple FAT block reads.

#### Indexed Allocation
A partial way around this is to keep all block numbers in a contiguous table for each file. 

Each file has its own index block (or "i-block") which is a dedicated block that stores a list of disk block addresses.

![[Pasted image 20240428225113.png | center | 300]]

Pros:
- Good for direct (random) access. 
- Information for each file is kept in one area. 
- No external fragmentation. 

Cons 
- If we lose an index block, we have lost access to a whole chunk of the file. 
- Wasted space in the index block for smaller files. 
- File size limited by the number of indices in the index table.
	- We can extend this in a number of ways (say multilevel indexing) .

##### Extending Index Blocks
A technique that enables file systems to handle large files efficiently. If the file has more blocks than we can reference from the index block, we need to have some way of connecting to more index blocks. 

We can link index blocks together (like the linked allocation of files). Similar pros and cons. 
We can have multi-level indexed allocation. 
- The first level (called the “indirect block”) points to index blocks. 
- The second level points to the file’s data blocks.


| ![[Pasted image 20240428225412.png]] | ![[Pasted image 20240428225449.png]] |
| :----------------------------------: | :----------------------------------: |

#### Index Blocks (Multi Levels)
If we have blocks of size 8K, and a block address of 4 byes, as well as a two level system then we can address files of up to 32 gigabytes. 

The indirect index block points to 2048 index blocks 
Each index block points to 2048 actual blocks
So we can have 4194304 actual blocks in a file. 

![[Pasted image 20240502090154.png | center | 400]]

### Unix Index Block Scheme
In order to minimise the number of blocks read to find the actual block (especially with small files) several versions of UNIX don’t use extra indirect blocks until necessary.

• NTFS goes one step further and stores small files < 1K in the MFT file record itself.

![[Pasted image 20240502090449.png | center | 400]]
### NFTS Extents
NTFS keeps all index information in the MFT. Rather than storing all block numbers it stores extents.

- A cluster is a number of blocks (2, 4, 8, etc), An NTFS volume is seen as an array of clusters.
- An extent is a start cluster number and a length (number of clusters).

![[Pasted image 20240502090938.png | center | 400]]
### Tracking free blocks
Whenever a new block is required, we need to be able to check if there is an empty block available. 

There are lots of possible blocks,  a 6TB disk with 4KB blocks would have about 1.5 billion blocks.

Early versions of UNIX maintained a device wide free list of blocks in a stack, where by the next allocated block is the most recently returned one.

This has several downfalls:
- You are not able to assign contiguous blocks as the order is going to be semi random
- On the hardware level, the same few blocks are going to be used repetitively. massively reducing the lifespan of certain blocks. 

An alternative to using a stack is called **Linked allocation**
We could link free blocks together like a large empty file using the linked allocation method.

With a linked list it is trivial to find the first free block. But it is not efficient if we want several blocks at a time, especially if we want them to be contiguous.

![[Pasted image 20240502091505.png | center | 100]]

**Bitmaps:**
We can maintain a bitmap (or bit vector). Where each bit represents a used or free block (1 represents free).

For the example image this takes up about 200 Megabytes of space. (6TB with 4KB blocks)

The free-space bitmap would be: `001111001111110001100000011100000...`

**Grouping Blocks:**
We can keep a list of start points and lengths (like NTFS extents). Bit map can then be used to track the availability of clusters/extents.

