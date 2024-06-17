
### Runtime file data structure
The system maintains several data structures when it is using a file

System wide open file table:
- The system must keep track of all open files.
- Information from the on-disk file control block (FCBs) (these must be kept consistent).
- Which processes are accessing the file?
- How is the file being accessed?

Process open file table
- A pointer to the system open file table.
- Current file position (for sequential reading or writing).
- A pointer to the buffer in memory that is being used for this file by the process.

File buffer
- Data is read or written in block (or cluster) amounts.

![[Pasted image 20240502091949.png | center | 400]]

### Opening Files
• Initiate Open Call
• Check If File Is Already Open (Consult system wide open file table)
• Create Process-Specific Entry
• Search Directory Structure
• Load File-Control Block (FCB)
• Update Process's Open-File Table
• Return File Descriptor or Handle
• File I/O Operations

![[Pasted image 20240502092405.png | center | 400]]
(a) open (b) read

![[Pasted image 20240502092648.png | center |400]]
Here we see
- the on-disk inodes and data blocks
- the in-core copy of the inode, this includes the reference count
- the system wide open file table (file-structure table), this maintains entries for each open file in the entire system.
- the process open file table, just an array of pointers to the file-structure table

When we use fork, the process specific table is copied. 
![[Pasted image 20240502092855.png | center |400]]

#### System Call
Most systems require some open call to make the connection between a process and a file.

The open call does several things (not all OSs do all of these): 
- Searches for the file with that name (in the given directory or path)
- Verifies that the process has access rights to use the file in the way specified 
	- We don’t check after this 
	- Can be a security problem, the TOCTTOU (time of check to time of use) problem 
- Records the fact that the file is open and the process using it (in the system-wide open file table) 
- Constructs an entry in the process open file table 
- Returns a pointer (file handle or file descriptor) to be used for future access

In Unix this looks like 
```c
// open(path, type of open)
fd = open("OS/test/answers", O_RDWR);
```

- This converts the filename to an inode, also copies the on-disk inode into memory and locks it for exclusive access. 
- If file does not exist or not permitted access, return error (ensure inode is not locked).
- Allocate system-wide file table entry, points to in-core inode, increment the count of open references to the file.
- Unlocks the inode 
- Return the index into the per-process file table entry (known as the file descriptor `fd`)

### Writing Files

```c
write(fd, buffer, count);
```

`fd` is the file descriptor returned from opening the file. 

```
BEGIN 
- Get file table entry from fd 
- Check file accessibility 

Lock inode <---- 

WHILE not all data is written 
- IF a block does not exist for the current position 
	- Allocate a new block and update the inode 

- IF not writing a complete block 
	- Read the existing block into memory 
	
- Place the data into the block's buffer 
- Mark the block for delayed write (to be written at a later time) 

Update file offset and amount written 
Update file size 
Update modification time 
Unlock inode <---- 

END
```

![[Pasted image 20240504191642.png | center ]]

#### Delay Write
Buffers are shared by the system, the write won't occur until another process needs to use the buffer for a different block ([[01 Storage Management#Replacement Least Recently Used (LRU) |LRU Replacement]]) or daemon process flushes it. 

This has the advantage of batching other writes to that block which improves performance. 
If a process wants to a this information it is already/still in memory. 

This however risks loosing data if the system crashes or the volatile memory looses power. A daemon process writes data buffers after 30s, metadata after 5. 

`sync()` or `fsync()` command forces buffers to write


### Unix Append
If the file has been opened in append mode `O_APPEND`, then there is a possible [[08 Concurrent Programming#Race Conditions|race condition]]

Before each write, the file position pointer is moved to the length of the file.  
If another write changes the length of the file before this write completes then there is the race condition. 


The file system must guarantee atomicity for the append write operation. That is why there is an append mode (`O_APPEND` flag) for opening a file.
