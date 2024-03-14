## What is an OS
An Operating System is a special computer program that sits between the User Applications and Hardware
![[Pasted image 20240226121645.png | center | 200]] 

We need an OS to provide a layer of hardware abstraction over, Allowing programs to run without having to worry about the underlying hardware. 

It also provides common services like filesystems, networking, process management, network managing etc. 

>[!info] Textbook definition
>A Operating system is a program that controls the execution of application programs and acts an an interface between applications and computer hardware.


Learning about operating system allows us to better use our own operating systems, to be knowledgeable about computers and computer systems, etc. 

### Kernel
The kernel is the core of an operating system, it is running all the time. 

#### Some kernels
Linux: https://www.kernel.orgKernel 
MacOS: https://github.com/apple-oss-distributions/xnu
Old Windows: https://github.com/0x5bfa/NT5.1

#### Duel Mode
**Kernel mode:** All CPU instructions are available and access hardware using IO instructions.  
**User mode:** Only a small set of instructions can be used and no hardware access.


If an application wants to run code on hardware, it needs to make a system call. This is a dedicated instruction (`syscall` in x86-64 cpus) which triggers a mode transition.

![[Pasted image 20240226122740.png | center | 500]]

Duel mode operation is implemented by CPU Hardware, on an x86 core there are multiple rings of security

![[Pasted image 20240226122836.png | center | 300]]

Ancient OSs (e.g., windows 3.1/9x) do not follow the dual-mode operation strictly, A user program may be able to access everything and wreak havoc on your PC. Kernel runs in ring 0, most OS do not use ring 1 or 2. 


### Kernel (OS) design style
There are three different styles for designing a kernel

#### Monolithic
- The kernel provides as many function s as possible
- The kernel is a single, large executable that includes various system services.
![[Pasted image 20240226123054.png |  center | 300]]

#### Microkernel
- Kernel does as minimum as possible. 
- Push more functions to user mode.
- ![[Pasted image 20240226123136.png | center | 300]]

#### Monolithic vs MicroKernel
Each have their pros and cons, there isn't a consensus on what it is. 

Monolithic: 
- Pro: Higher performance (less user to kernel transition) 
- Con: One component fails, everything fails. 
- Example: Linux  
Microkernel: 
- Pro: Better stability and security 
- Con: Higher performance overhead. 
- Example: Mac OS

#### Hybrid kernel
This is what windows is based off of
![[Pasted image 20240226123329.png| center ]]

### OS Features
#### Multitasking
There are always multiple tasks running at the same time on a computer
Jobs are selected on the CPU via scheduling
If it switches fast enough (<10ms) we feel like multiple jobs are running simultaneously. 

#### Virtual memory
Every program thinks it has an unlimited amount of memory, they cannot see other programs memory content by default. A virtual address is mapped into physical address by hardware.

![[Pasted image 20240226123552.png | center  | 400]]

![[Pasted image 20240301111910.png | center | 400]]
16 Million TB is the theoretical maximum amount of memory that a computer can have. 
#### File management
- Tree-like structure 
- Data integrity checks 
- Compression 
- Security

#### Security
- User authentication 
- Access Control 
- Encryption 
- Firewall

### Loadable Modules
Many modern operating systems implement loadable kernel modules (LKMs)
- A module can be loaded and unloaded upon demand.
- Each module can talk to the others over known interfaces
- Extend the functionality of the kernel easily.

#### Linux Kernel Module
Linux kernel modular design facilitates us to
- Add a device driver to control LED lights
- Add a device driver to spoof GPS locations
- Add a filesystem
- Add a network protocol
#### Windows Kernel driver
![[Pasted image 20240301110355.png | center | 300]]
One example of a windows kernel driver are anti cheats like easy anti cheat or vanguard.

#### Writing kernel modules
Kernel modules are written in C
- The C language was originally designed to build Unix.
- C provides low-level access to memory
- C can be easily mapped to machine instructions
