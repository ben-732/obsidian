### Main Memory
All processes need main memory.

Instructions and data being worked on are brought into the processor from memory and sent back to memory.

Traditional view:
- Addresses are numbers: `0` – `maxMem`
- An address goes out of the processor on the address bus to the memory store
- The value comes back on the data bus

### Memory Spaces
Even in simple systems, we would like a way of protecting OS memory from our program and enabling programs larger than memory to run.

Split memory
- Can protect with a single fence register.
- Alternatively, if the OS is in ROM its code and constant data is safe from overwriting.

Overlays
- Load needed instructions over the top of ones
- Not needed any more as memory is not as limited.
#### Overlays
 Some code and data is always maintained in the memory of the process.

Other areas can be removed and overlaid with another overlay.
- Base (always in memory), with overlay A, then B, then A.
- e.g. program access goes from Base, Control, A, Base, Control, B, Base, Control, A, Base

This makes programming more complicated as you need multiple fence registers to seperate the memory

### Dividing Memory
With relocating loaders we can have multiple programs in memory simultaneously.
We can have separate partitions of memory with base and limit registers.

- Both require contiguous memory.
- The algorithm for base and limit registers is simple.
- If the address is less than the base or greater than the base + limit – 1, then we have an access violation.
- The base and limit registers are loaded for each process.
- We need a lot more memory, maybe more than we have.
- So we could use overlays in each allocated area.

![[Pasted image 20240608140444.png | center | 300]]


One method for doing this is comparing an address with base and base + limit. 
![[Pasted image 20240608140804.png | center | 300]]

If we change our base and limit system to produce the address by adding the base register (now called a relocation register) to each address, we can make all processes start at address 0. So, no need to translate addresses at load time.

![[Pasted image 20240608140912.png | center | 500]]

### Two different addresses
This is a large conceptual change.
We now have two types of addresses:
- The logical (virtual) address coming out of the process, produced by the CPU
- The physical (real) address used on the address bus to memory.

We still have contiguous memory for each process, but a process can now be positioned anywhere in physical memory without its addresses needing to be changed.

We can even move a process around, just copy the memory to the new place and change the relocation register. This is useful if we want to defragment memory to give one large free
area.

Have to be careful if moving data (e.g. from a device) into the process’ memory space.

### Smaller memory chunks
Rather than moving memory around to make big enough spaces for processes we could have more than one section of physical memory accessed by the same process.

We need either a number of base-limit registers or a table of the information.

![[Pasted image 20240608141441.png | center | 400]]
#### Two Approaches
This technique evolved in two directions.
1. Same sized chunks – pages
2. Variable sized chunks – segments

- Both have advantages and disadvantages.
- Both use Memory Management Units (MMUs) in hardware to do the translation between the logical and the physical address.

Rather than doing a tedious calculation to find what chunk an address is in, we split the address into two parts. Then the translation is much simpler and looks very similar in both paged and segmented systems.

#### Address Translation
In a paged system, addresses are split into:
- **Page number:** index into a page table which contains base address of each page in physical memory
- **Page offset (displacement):** added to base address to get the physical address.

![[Pasted image 20240608142522.png | Center | 500]]


In this case there is a constant number of bits for the offset. This means that pages are always powers of 2 in size – usually from 2048 (11 bits) to 8192 (13 bits). 

Some systems allow variable sizes of pages, including very large pages.

#### X86 Variable sized page 
![[Pasted image 20240608151456.png | center | 400]]

![[Pasted image 20240608152737.png | center | 400]]
#### ARM multi-level indexing system
![[Pasted image 20240608153107.png | center | 400]]
### Frames and Pages
A frame is a page sized chunk of physical memory that can be allocated to a page from a process.

A page is a frame sized chunk of logical memory that fits in a frame. It is common to refer to both simply as pages (physical and logical).

Fragmentation
- No external fragmentation between frames.
- Internal fragmentation in any pages that are not completely used.
- Could be an average of ½ a page per process area (or ½ a page per thread, ½ a page for heap space, ½ a page for code, ½ a page for initialized data, etc ).
- Small pages save space but require larger page tables.

In x64 machines it is possible to use page sizes of 1GiB (but normally we work in 4KiB pages).

### Tables
Each process has its own page table. (CR3 for Intel)

And commonly the OS has a frame table with one entry for each frame showing whether it is free or not and which process is currently using it.

![[Pasted image 20240608155912.png | center | 200]]
### Segments
>Variable sized chunks.

Rather than constant sized pages we could design our hardware to work with variable sized chunks – these are known as segments.

(Not to be confused with variable sized pages.)

**Memory model**
How memory appears to be organised to the program (and programmer) is sometimes referred to as the memory model.

A segmented memory model is when we look at memory as a group of logical units all with addresses starting at zero.

Processes can have lots of segments
- Functions
- Global variables
- Activation records
- Large data structures (arrays)
- Objects

These logical units fit nicely into hardware specified segments where different amounts of memory can be allocated in one chunk.

A segment is a contiguous block of memory, because of this we get problems of external fragmentation.

Our memory addresses become `<segment name, displacement>`.

The translation process is similar to paging, except there is a length associated with each segment. We have a segment descriptor table rather than a page table.

The physical address is the result of an addition rather than a concatenation (like in a paged system.)

![[Pasted image 20240608160638.png | center | 400]]
#### Allocation Strategies
Segments don't have internal fragmentation – as we only allocate the amount of space we want.
However, there is external fragmentation. 

We have seen the allocation strategies before:
- First fit
- Next fit (first fit but starting from where we were up to)
- Best fit
- Worst fit

We can defragment (coalesce) memory if we want to find large enough chunks. Faster than defragmenting disks.

![[Pasted image 20240608161218.png | center | 300]]

#### How much fragmentation
Knuth’s 50% rule (textbook 9.2.3)

If there are n segments there are n/2 holes.

Each segment is either below another segment or below a hole.

We always combine adjacent holes

Each segment is released independently – so in a steady state system the space above each segment will alternate between being used and being free. 50% of the time there will be a hole above each segment. If the average size of a hole is the same as the average size of a segment, we need about ==1/3 of the memory== free to keep this system running.

### Half speed memory
In both paged and segmented memories every logical memory access requires (at least) two memory accesses. 
- One for the page/segment table entry
- One for the actual data.

It is possible the number of segments may be quite small and there may be registers for them.

The MMUs cache recent page table information in a special fast-lookup hardware cache called associative registers or translation look-aside buffers (TLBs)

![[Pasted image 20240608162433.png | center | 300]]

You can either:
- Find the page number then access the physical memory.
- Fail to find the page number then access the page table Access the physical memory and Update the TLB
#### Average Access times
![[Pasted image 20240608163455.png | center | 450]]
#### TLB Coverage
The coverage (or reach) is the amount of the address space included in the TLB entries.

Typical TLB caches hold between 32 and 1024 entries.
- With 4K pages this is only 4 megabytes of memory.

As working sets increase, some processes have a real performance hit, memory wise.

One solution is larger page sizes. This means more internal fragmentation. More IO (in virtual memory systems).

Variable page sizes can be used but they need good allocation algorithms to be worthwhile.

### Page Table size
 Another problem with page tables is their potential size. 
 
 With 32bit address space and 4Kbyte pages (offset of 12 bits). There are 20 bits to index into the page table ≈ a million entries (at least 4Mbytes for each process, depends on PTE(Page Table Entry) size)

- Most processes do not use all memory in the CPUs logical address space. 
- We would like to limit the page table to values that are valid.
- Only allocate the parts of the page table we actually need.
- Can do this with a page table length register.
- Can flag page table entries with a valid bit.
- Page the page table (see virtual memory)

#### Multi Level page tables
Where there are multiple levels to the page table, the number of reads for one access increases. 

Because of this, we need a really good TLB hit ratio
Only have to store relevant tables in memory
![[Pasted image 20240608164044.png | center | 300]]
#### Hashed page table
Use hash function to distribute, on collision make linked list. 
![[Pasted image 20240608164523.png | center | 300]]

#### Inverted Page table
As the number of address bits increases to 64, we need even more levels of page tables.

Another approach is to keep information about the physical pages (or frames) rather than all of the logical pages. This is known as inverted page tables.

Only need one page table for all processes. Each entry needs to refer to the process that is using it and the logical address in that process.

A logical address is `<pid, page number, displacement>`

Have to search the page table for `<pid, page number>`. Use hash table for the page table and rely on TLBs.

![[Pasted image 20240608164856.png | center | 300]]
### Large Programs
It is very common for programs to be bigger than system memory. 

This was handled early on by overlays. These required care on the part of the programmer to split the program up into distinct sections and any connection between the sections had to be carefully worked through.

It became even more of a problem with multiprogramming and several programs occupying memory.

![[Pasted image 20240608170246.png | center | 500]]

Drawbacks of this overlay approach include
- Programming complexity
- Loading overlay modules from external storage into memory actually trades off increased time for saved space.

### Process Swapping
With multiprogramming we can swap entire processes out to disk to provide space for others (and swap back in to run).

The disk is known as backing store and it must be able to hold all memory for all processes.

Swapping is slow – especially if done at every process context switch.

Does the process have to be swapped back into the same memory space? – Relocation Register.

>"Swap space" or "backing store" is the area of a disk allocated to hold pages of memory



### Overlay v.s. Process Swapping
**Swapping:** between programs in the memory and OS.
**Overlay:** Inside the program being executing. Programmers should explicitly the hierarchy of overlay structure.

What we need:
- No increased complexity in program design (compared to Overlay)
- Efficient use of disk I/O to avoid swapping entire processes, which can be time-consuming. (compared to Process Swapping)
- Providing the illusion of having more memory than physically available, allowing for larger programs and more efficient multitasking.
- Increasing system stability and security by isolating process memory from each other. (not a must)

### Virtual Memory
We can execute programs without the entire program being in memory at once
Can keep either pages or segments on disk when not needed.
The logical (virtual) address space can be larger than the size of our physical memory.

This has many advantages:
- Unused code doesn’t waste physical memory
- We have more memory for multiple processes
- We don’t need to load the whole program into memory at once –hence speeding up responsiveness to commands (demand paging)
![[Pasted image 20240608171323.png | center | 400]]

### Locality of Reference
In almost all programs if we look at their memory access over a short period of time (a window) we see that only a small amount of the programs address space is being used.

Each memory access is very probably going to be near another recent memory reference.

This is true for both code and data.

It is possible to write programs that don’t do this. e.g., arrays stored as rows, but accessed by columns.

This is known as the principle of locality of reference. Programs do not reference memory with a random distribution.

### Virtual Memory Paging
Virtual memory is commonly provided with paged memory.

There are extra bits stored in each page table entry (and some of them in corresponding TLB entries).
![[Pasted image 20240608171748.png | center | 300]]
V – valid bit, is the page currently in real memory?
A – access bits – how can this page be accessed, read/write/execute?
M – mode bits – which mode does the processor have to be in when it uses this page
D - dirty bit - has this page been modified since this bit was last cleared?

Other bits could be there too. e.g., referenced bit address – either the frame number or the address on the disk device where this page is currently stored (this could be stored in a separate table)


So, when a page is accessed, the page table entry indicates whether the page is currently in real memory or whether it is in a paging file (or swap space) on disk.

The MMU happily takes care of the translation between logical addresses and physical addresses when all pages are in real memory.

If a page is not in real memory, it is up to the OS memory management system to
- Allocate real memory for the page
- move pages from disk into memory
- Indicate when the page is now ready

### Demand Paging
Demand paging chooses when a page gets loaded into real memory.

When a process starts, all of its memory can be allocated (and loaded).

If there is not enough real memory available, it has to be taken away from pages currently used

If there is still not enough, some has to go into swap space
- Loading a large program can have a severe penalty on other processes in the system (and the overall amount of work done)

Demand paging only brings a page into real memory when the page is used by the process.
- When a process runs, it is allocated memory space but it all points to the swap space (or somewhere else)
- Most demand paging systems do load in the first few pages so that the program can start without lots of page faults

### Page Faults
If ever a memory access finds the valid bit of the page table entry not set, we get a page fault.
- The processor jumps to the page fault handling routine.
- Checks if the page is allocated (if not, we have a memory violation).
- If allocated (but not in a frame)
	- Find a free frame (possibly create one)
	- Read the page from the swap space into the frame
	- Fix the page table entry to point to the frame
- If the page is shared, then multiple entries must be fixed
- Restart the instruction that caused the fault
- Instructions must be restartable

![[Pasted image 20240609124009.png| center | 400]]

If we don’t have enough physical memory:
- Page replacement: If frame `f` (page `q`) is modified, write it back first.
- Change “v” to “i” for page entry (page `q`).
- Transfer the demanded data from backing store to physical memory.
- Modify the page entry (page `p`).
- “invalid” -> “valid”. 

Now page `p` can be referenced to frame `f`

We only want this to occur one in every roughly 600,000 memory accesses. This can either be achieved through having lots of frames or lots of repeated accesses. 

### Reducing Page Faults
Different processes have different memory access patterns and therefore different numbers of pages they need to have in memory at one time.

There is a minimum number we must have otherwise this instruction may never complete.

Frames can be allocated equally or proportionally depending on size or priority. 
- Can set minimum and maximum numbers per process.
- We really need the currently required pages in real memory.


#### Working sets
This is relating to locality of reference. 

The working set of a process is the collection of pages needed in real memory in order to keep the process running.

If we observe a process running over a short period of time (a window) we can record the page accesses the process makes. This is a picture of the page’s working set.

The trick is getting the window the right size:
- If it is too small not enough pages are included in the working set
- If it is too big too many pages are included
![[Pasted image 20240609125124.png | Center | 400]]

##### Approximations
Approximate with interval timer + a reference bit
A reference bit is available in the page table entry in some architectures to indicate the page has been accessed (read or written) since it was cleared.

Example: window = 1,000ms, Timer interrupts after every 500ms.

Keep in memory 2 bits for each page.
- Whenever the timer interrupts read and then set the values of all reference bits to 0.
- If one of the bits in memory = 1 ⇒ page in working set.

#### Page Fault Frequency
Used to control the number of frames allocated to a process.

As the number of frames increases the number of page faults drops rapidly at first, then reaches a point, where adding more frames hardly alters the rate at which paging occurs. We set upper and lower bounds and add or remove frames to stay within them.

![[Pasted image 20240609130318.png | center | 300]]

### Choosing frames for replacement
When there are no free frames to bring in a page, the system has to pick one to replace

There are two main ways of selecting frames for replacement.
- Global – any frame allocated to any process can be chosen
- Local – chosen frames must come from the processes own allocated frames

With the global method the number of frames for a process varies depending on its behaviour and the behaviour of the other processes. (The same process can run with widely varying speed due to other processes taking some of its frames.) With the local scheme there are less frames to choose from.

Normally global replacement is chosen, but we might have a maximum number of frames for the process.

Frames chosen for replacement preferentially:
- Pages that are read-only or haven’t been modified don’t have to be written back to disk (this saves on swapping time)
- Page table entries commonly have a dirty-bit to indicate the frame has been changed since the page was loaded
- Pages that aren’t going to be accessed again in the near future (so we don’t end up with another page fault on the page we just moved out) – unfortunately we can’t see into the future so we rely on recent behaviour.

If we have a referenced bit, we might use this to get an approximation (or use a window to see if a page is in a working set)

There are selection algorithms, for each algorithm we want lowest page-fault rate.

Evaluate algorithm by running it on a particular string of memory references (reference string) and computing the number of page faults on that string.
#### Random algorithm
It treats every process fairly
- Easy to implement
- With enough pages the method won't replace pages just about to be used too frequently
![[Pasted image 20240609131007.png | center | 300]]
#### Fifo
First in first out - Keeps a list of pages in a queue. 
- Simple
- Very important pages (such as part of the operating system) which are referenced frequently will be paged out just as frequently as pages which are hardly ever referred to

Belady’s anomaly – increasing the number of frames occasionally increases the number of page faults

#### LRU
Least Recently Used,  Based on the assumption that a page not used recently will not be used in the near future. Generally better than FIFO.

Very expensive – need to have hardware that keeps track of last access time for each page.
Or maintain a list of pages and move a page to the top of the list when accessed.

#### Like LRU
Use the **referenced bit** – originally clear, set when the page is used.

Have a reference byte to act as a history. Every 100ms shift right, write the reference bit to the MSB. The pages with the lowest numbers have either been used the longest time ago (or not used as regularly).

#### Second chance/clock page replacement
**FIFO** – but if a page has a 1 in its referenced bit when it is chosen, we don’t replace it, but clear its referenced bit instead (and change its arrival time to be now).

Commonly implemented as a circular queue.

![[Pasted image 20240609132442.png | Center | 400]]
#### Least Frequently used (LFU)
Mmaintain a count of memory accesses for each page
- Keep heavily used pages
- Pages can stay around after they are needed – can decrease the count over time.
- New pages get picked on.

#### Most Frequently Used – MFU
Pages with very few accesses may have just been brought in to memory.
Neither is commonly used.
#### Death Row
• Put frames into a replacement pool according to FIFO selection.
• Keep track of which page is in each frame.
• If a page is accessed while its frame is in the replacement pool, then retrieve it.
• There is no penalty for paging from disk in this situation. (soft fault)

#### Optimal 
If we can see the future, the optimal algorithm replaces a page which is going to be used furthest away in the future.

If pages are not going to be used again then FIFO on those is suitable.

