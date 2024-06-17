# What is an OS
An Operating System is a special computer program that sits between the User Applications and Hardware
![[Pasted image 20240226121645.png | center | 170]] 

We need an OS to provide a layer of hardware abstraction over, Allowing programs to run without having to worry about the underlying hardware.  It also provides common services like filesystems, networking, process management, network managing etc. 

>[!info] Textbook definition
>A Operating system is a program that controls the execution of application programs and acts an an interface between applications and computer hardware.

Learning about operating system allows us to better use our own operating systems, to be knowledgeable about computers and computer systems, etc. 

**Kernel:** The kernel is the core of an operating system, it is running all the time. 
#### Duel Mode
**Kernel mode:** All CPU instructions are available and access hardware using IO instructions.  
**User mode:** Only a small set of instructions can be used and no hardware access.

If an application wants to run code on hardware, it needs to make a system call. This is a dedicated instruction (`syscall` in x86-64 cpus) which triggers a mode transition.

![[Pasted image 20240226122740.png | center | 350]]

Duel mode operation is implemented by CPU Hardware, on an x86 core there are multiple rings of security
![[Pasted image 20240226122836.png | center | 220]]

Ancient OSs (e.g., windows 3.1/9x) do not follow the dual-mode operation strictly, A user program may be able to access everything and wreak havoc on your PC. Kernel runs in ring 0, most OS do not use ring 1 or 2. 
### Kernel (OS) design style
| Monolithic                                                                                                                           | Micro kernel                                                                                             |
| ------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------- |
| The kernel provides as many functions as possible<br>The kernel is a single, large executable that includes various system services. | Kernel does as minimum as possible. <br>-Push more functions to user mode.                               |
| ![[Pasted image 20240226123054.png \|  center \| 220]]                                                                               | ![[Pasted image 20240226123136.png \| center \| 270]]                                                    |
| **Pro**: Higher performance (less mode transitions) <br>**Con**: One component fails, everything fails. <br>**Example**: Linux       | **Pro**: Better stability and security <br>**Con**: Higher performance overhead. <br>**Example**: Mac OS |
Each have their pros and cons, there isn't a consensus on what it is better. 

A **Hybrid Kernel** is a combination of the two, this is what windows is based off.
![[Pasted image 20240226123329.png| center | 440 ]]
### OS Features
#### Multitasking
There are always multiple tasks running at the same time on a computer
Jobs are selected on the CPU via scheduling
If it switches fast enough (<10ms) we feel like multiple jobs are running simultaneously. 
#### Virtual memory
Every program thinks it has an unlimited amount of memory, they cannot see other programs memory content by default. A virtual address is mapped into physical address by hardware.

 The theoretical maximum amount of memory that a computer can have is 16 Million TB. 

| ![[Pasted image 20240226123552.png \| center  \| 300]] | ![[Pasted image 20240301111910.png \| center \| 190]] |
| ------------------------------------------------------ | ----------------------------------------------------- |
#### OS Services:
**File management:** Tree-like structure, Data integrity checks, Compression, Security
**Security**: User authentication, Access Control, Encryption, Firewall
### Loadable Modules
Many modern operating systems implement loadable kernel modules (LKMs)
- A module can be loaded and unloaded upon demand.
- Each module can talk to the others over known interfaces
- Extend the functionality of the kernel easily

| Linux Kernel Module                                                                                                                                                           | Windows Kernel driver                                                                                                                             |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| Linux kernel modular design facilitates us to do things like:<br> <br>Add driver to control LEDs<br>Driver to spoof GPS locations Add a filesystem<br>Add a network protocol. | ![[Pasted image 20240301110355.png \| center \| 200]]<br>One example of a windows kernel driver are anti cheats like easy anti cheat or vanguard. |
Linux Kernel modules are written in C
- The C language was originally designed to build Unix.
- C provides low-level access to memory
- C can be easily mapped to machine instructions
# C Programming
### Pointers
A Pointer is an address in memory. C pointers can have any integers assigned to them
- `int *p = 0xfff4;` `*p = 50;`

This means that actual addresses can be stored in a pointer and then used to access content over there. Using pointers incorrectly can create big errors.
#### Segment Fault
If a pointer tries to access an inaccessible address in a user-space program.
```c
int main() {
	int *pointer = 0xdeadbeef;
	printf("content in that pointer %d\n", *pointer)
}
```
The OS can catch this because user-space programs run in [[01 Intro to OS#Virtual memory |virtual memory]], so it has to be mapped into physical memory if there is no way to map it then there will be an error. 

![[Pasted image 20240301111817.png | center | 300]]
#### Kernel Panic
If the pointer tries to access an invalid pointer in kernel space.
#### Dangling Pointers
When you make a pointer to a variable and then the variable gets de-initialised. This means that the pointer is pointing to an invalid address.  Creates a `use-after-free` error

Stack based dangling pointer:
```c
int* q;

void foo() {
	int a;
	q = &a;
}

void main() {
	foo();
	... = *q;
}
```
![[Pasted image 20240311085438.png | center | 120]] 
#### Memory Management 
- **Local variables** for a function are allocated on the stack when the function is entered, Local variables are gone when the function finishes execution.
- **Global variables** are allocated in data area when the program is started. They are only released when the program exits.
- **Static variables** are allocated when the program starts, they are also only released when the program exits. 
```c
int global_var = 0;

int main() {
	int local_var = 1;
	static int static_var = 2;
	
	return 0
}
```
#### Dynamic Memory
Local variables, static variables, or global variables all have a fixed size.
- To allocate a memory chunk with a dynamic size in a user program, use `malloc`.
- Remember to free them using `free`, when they are not longer used.
```C
int main() {
	int *array = (int*)malloc(1024 * sizeof(int));
	...
	free(array);
}
```
#### Memory Layout
![[Pasted image 20240301112859.png | center | 300]]
### Accessing registers
You can use the keyword `register` to hint that a variable should be kept in a processor register to reduce data access latency.
- `register int number = 1234;`

This does not guarantee that a value is stored in a register. Compilers may opt not to do so. Compilers do better than programmers in terms of register allocation (most of the time)
### Volatile Variables
You can use the keyword volatile to specify that a variable may change unexpectedly., This requests compilers not to do optimisations on that variable. `volatile int a = 10;`
![[Pasted image 20240301113131.png | center | 300]]

The other use case of volatile allows you to share a region of memory with other hardware. This will tell the compiler that this memory might be updated externally. 
### Inline Assembly
![[Pasted image 20240301113416.png | center | 200]]
### C Compilers
There are two main options available
- **GCC:** almost 40 years old, very stable, the "gold standard"
- **Clang (LLVM):** Modern architecture, faster compilation times, more advanced optimisations, but less stable. Not often used for OS dev but common in user-space dev

Compile using gcc `gcc path/to/file.c`
Generates by default `a.out`, to run: `./a.out`

`gcc file.c -g -02`:  `-g` attaches debugging, `-02` do optimisation on source code for better binary.
#### GDB
GDB is a debugger for C, it works with `-g` GCC compiled files. Gives good debugging info
#### LLVM Compiler infrastructure
The LLVM Infrastructure Provides the availability for you to make your own language. Just write a few hundred lines of code. You just write the frontend, and LLVM handles the rest of the conversions

![[Pasted image 20240301113701.png | center | 300]]
# Kernel Module Development
The program that takes input from the user and gives them to the operating system to preform. 
For some tasks it is more convenient than using a GUI
1. Get a virtual machine 
2. Install a linux OS
3. Install the C compiler and necessary C headers 
	- `sudo apt-get install build-essential linux-headers-$(uname -r)`
4. Create the source code
5. Create the makefile
6. Debug and test
#### Virtual machine
Use a linux virtual machine to protect your system as mistakes could crash / destroy your OS.

How VMs work:
1. Fetch: Get a GBA instruction from the game. (For example: ADD R3, R3, #5) 
2. Analyse: It adds 5 to the register R3. 
3. Translate & Execute: register_3 = register_3 + 5 
4. Go to step 1.

| Interpretation                                                                                                                                                                                       | Binary Translation                                                                                                                                                                                    |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Interpretation**: fetch a CPU instruction, analyse it, perform the operation<br>**Pro** Can execute code for different CPU architectures.<br>**Con**: high execution overhead for each instruction | Binary translation amortises the fetch and analysis costs by<br>1. fetch and translate a block of source instructions to target instructions. <br>2. Saving the translated code for repeated use.<br> |
Generally binary translation has much higher performance results, it is roughly 10x faster in most situations. 
![[Pasted image 20240305092323.png | center | 200]]
##### Hardware Virtualisation
- Translation and analysis are unnecessary if code is compiled for the same CPU architecture. 
- Consider running a Ubuntu x86-64 on a Windows x86-64 machine. 
- Ubuntu is the guest OS, Windows is the host OS

Modern CPUs use dedicated circuits to isolate programs between host and guest OS. Secure Virtual Machine (SVM) in AMD, Virtual Technology (VT) in Intel. This results in very low overhead. 
#### Source Code
```c
#include <linux/init.>
#include <linux/module.h>
#include <linux/kernel.h>

MODULE_LICENSE("GPL");
MODULE_DESCRIPTION("A simple kernel module");
MODULE_AUTHOR("Name");
MODULE_VERSION("0.1");

static int __init hellow_init(void) {
	printl(KERN_INFO "Hello, world!\n");
	return 0;
}

static void __exit hello_exit(void) {
	printk(KERN_INFO "Goodbye, world!\n");
}

module_init(hello_init);
module_exit(hello_exit);
```

`MODULE_AUTHOR`: Identifies the author of the module. 
`module_init` is used to specify the entry point of a module. 
`module_exit` defines the function that will be called when the module is unloaded from the kernel. 

`printk` is designed to output kernel logs at different priority levels. 
- `printk(KERN_INFO "Message: %s\n", arg);` 
- Alias: `pr_info()`
#### Makefile
```shell
obj-m += dummu_module.o

all: 
	make -c /lib/modules/$(shell uname -r)/build M=$(pwd) modules
```
#### Debug and test 
1. `make`: Compile your source code into a module. (`hello_world.ko`) 
2. `insmod`: Load a module. (`insmod hello_world.ko`) 
3. `dmesg`: Show the kernel log. 
4. `rmmod`: Unload a module

Makefile is a script to automate the process of building executable programs. 
- `obj-m += dummy_module.o` Specifies the source file for the module

#### Everything is a file
In linux (at least) everything is a file, this includes all hardware/devices etc,
- Keyboard is `/dev/input/keyboard1`, Mouse is `/dev/input/mice`,  Nvidia GPU is `/dev/nvidia`, Random number generator `/dev/urandom`, Zero generator `/dev/zero`
### Odd/Even Example
In the initialisation function, the module registers the device as a file using `register_chrdev` functions. 

A function argument is `file_ops`, which specifies what to do when the file is opened, read, written and closed

![[Pasted image 20240305094347.png | center | 400]]
#### Device write example
In the device write function, we accept an unsigned integer from user. 
- Using `copy_from_user` function to ensure the given buffer is valid.
![[Pasted image 20240305094525.png | center | 500]]
#### Read Function 
In the device read function, we return a result (0 or 1) to indicate whether the given number is odd or even.
![[Pasted image 20240305094656.png | center | 500]]
#### Interacting with device
- The device exposes itself as a file in /dev/odd_even
- We can use a simple python script to interact with it
![[Pasted image 20240305094751.png |center | 500]]
# Process Creation
A process is used to represent ongoing work in the system. More formally, an instance of a program in execution.
- A program: A program is passive entity stored on disk (e.g., exe file). It includes code and data.
- An instance: there may be multiple processes coming from the same program.
- In execution: It needs the processor to run.
- A process can own resources (CPU times, Memory, and Files)
On unix systems, you can use the command `ps aux` to get  list of processes. Alternatively, the `top` command shows all processes in real time. 
### Process control block (PCB)
A data structure where the OS can find all the information it needs to know about a process. 
A PCB Contains: 
- process identifier `pid` (usually a number)
- Process state (running, runnable, or waiting for IO)
- Program counter (location of instruction to next execute)
- CPU registers (contents of all process-centric registers)
- Memory-management information (allocated memory)
- Accounting information (CPU time used, clock time elapsed since start, time limits)
- O information (list of opened files)
![[Pasted image 20240311092727.png | center | 100]]
#### In linux
Linux uses a C structure task_struct to represent PCB
![[Pasted image 20240311092800.png | center | 500]]
### Creating a process
When the user starts a process,Internally the operating system has to: Allocate a new PCB, Memory Allocation, Setup the execution context (setting up entry point and registers), Initialisation of Process State, Execution of the Process

In linux, A parent process create children, which in turn, create other children creating a tree of processes. This is done with the `fork()`  system call.  `fork()` duplicates the parent process (the process that calls `fork`)

| ![[Pasted image 20240311093257.png \| center \| 250]] | ![[Pasted image 20240609142121.png \| center \| 100]]<br> |
| ----------------------------------------------------- | --------------------------------------------------------- |
Fork returns 0 in the child process and the child's pid in the parent. This allows us to tell whether the code is running in parent or child process. The child and parent process share open files as well.

In linux, a child process can use the system call `exec()` to replace its memory space with a new program.  We can combine `fork()` and `exec()` to create a new process. 

Coping a process using fork and immediately release it using exec seems like a bit of a waste.  Linux uses COW (copy on write) to address this issue. COW effectively just delays the copy until the value is modified.

To terminate the process immediately, call `exit()` Implicitly called, when return from the **main** function. `void exit(int status);`

A call to `wait()` blocks the calling process until its child processes exits or a signal is received.
- After child process terminates, parent continues its execution
A parent process can terminate a child process using the kill.
- `int kill(pid_t pid, int sig);`

```c
pid_t child_pid;

if ((child_pid = fork()) === -1) { // Create a child process
	perror("fork");
	exit(EXIT_FAILURE);
}

if (child_pid == 0) { 	// Child process
	printf("I am child\n");
	if (execlp("top", "top", (char *)NULL) == -1) { // Run the 'top' command in the child process
		perror ("execIp");
		exit(EXIT_FAILURE);
	}
} else { 	// Parent process
	printi("I am parent\n");
	wait(NULL); 	// Wait for the child process to finish
	return 0;
}
```

# Inter Process Communication
Each process in the modern operating system operates independently by default. Processes do interfere with each other's execution. Each process has its own address space and cannot directly access each other's memory. 

There are benefits in allowing processes to communicate:
- Computation speedup
- Information sharing and protection
- Modularity

There are two classic methods of IPC: Message Passing and Shared Memory. 
![[Pasted image 20240312191209.png | center | 180]]

### Pipe (Message Passing)
In Unix based systems, you can create  a pipe using `pipe()`. Pipes are treated as normal files. 

`int my_pipe[2]; pipe(my_pipe); fork();` Because the child and parent process are identical, they share opened files. 
![[Pasted image 20240312191420.png | center  ]]

Parent reads data from child `read(fd[0])`, Parent sends data to child `write(fd[1])`

**Empty / Full pipes**
When trying to read something from an empty pipe your program will freeze until data becomes available. Your program will also stop and wait if it is full (>64KB)

**Broken Pipes** (When one end has crashed)
A process waiting to read from a pipe with no writer gets an EOF
A process writing to a pipe with no reader gets signalled and killed. 
#### Named Pipes
- Named pipes are more powerful, no parent child relationship is required. 
- Several processes can use the named pipe for communication. Windows also implements a named pipe. 
- Use `mkfifo()` with a file path to create a named pipe. The pipe looks like a normal file and can be read/written by different processes. These have the trade off of being one directional.
#### Using Pipes
```c
int main() { // Writing
    const char *fifo_path = "my.pipe";
    mkfifo(fifo_path, 0666); // Create the named pipe

    int fd = open(fifo_path, O_WRONLY);// Open the named pipe for writing
    if (fd == -1) {
        perror("Error opening named pipe for writing");
        exit(EXIT_FAILURE);
    }
	// Write a message to the named pipe
    const char *message = "Hello from writer"; 
    write(fd, message, sizeof(message)); 

    // Close the pipe
    close(fd);
    return 0;
}

int main() { // Reading
    const char *fifo_path = "my.pipe";

    int fd = open(fifo_path, O_RDONLY); // Open the named pipe for reading
    if (fd == -1) {
        perror("Error opening named pipe for reading");
        exit(EXIT_FAILURE);
    }
	// Read from the named pipe
    char buffer[100]; 
    read(fd, buffer, sizeof(buffer));
    printf("Received message: %s\n", buffer);

    // Close the pipe
    close(fd);
    return 0;
}

```
### Internet Sockets
A socket is an endpoint for communication. A common form of socket is an internet socket.  It is identified by an IP address and a port.

The socket `10.0.0.1:1625` refers to port `1625` on host `10.0.0.1`

An IP(v4) address is a 32 bit sequence of 1s and 0s. Special IP address `127.0.0.1` (localhost) refers to the host itself. 

Ports below `1024` are well known and standard services, for example:
- `80` - HTTP, `443` - HTTPS, `20` TCP File transfer, `22` TCP SSH etc

##### Protocols
There are two main communication protocols. UDP and TCP

UDP is a connectionless protocol, It does not establish a connection before sending data, each packet is treated as an independent packet. There are no grantee for delivery or delivery order

TCP is a connection-oriented protocol. It needs to establish a connection channel before any data is transmitted. The connection is usually reliable and ensures the delivery order.

Because of this UDP is used in situations where low latency is important but TCP is used more frequently in other applications.

KCP is another protocol that has advantages from both sides 
- Reliable
- Low Latency
- User space (On top of UDP)

###### Setting up TCP Connection
Server process 
- `socket()` - make a socket, specify the domain and protocol 
- `bind()` - associate a name with the socket 
- `listen()` - now ready to get connections 
- `accept()` - gets a connection and returns a new socket (used for the actual communication)

Client process 
- `socket()` - make a socket, specify the domain and protocol 
- `connect()` - makes the connection between this socket and the named one

![[Pasted image 20240320151828.png | center |200]]
###### Setting up UDP Connection
This is a much more simple process than a TCP connection
![[Pasted image 20240320152046.png | center | 300 ]]
### Unix Sockets
Also known as the Unix domain socket. Using TCP and UDP for IPC is overkill as they are designed for separate machines to communicate.  Compared with an internet socket: Uses a file system path as an address, Limited to communication on the same machine, Generally has lower overhead and higher performance.

Has similar setup process to a TCP socket.  Receiver Side
```python
socket_file = "/tmp/socket_example"

server_socket = socket.socket(socket.AF_UNIX, socket.SOCK_STREAM)

server_socket.bind(socket.file) # Bind the socket to the filepath 
server_socket.listen(1) # Listen for incoming connnections

while True:
	connection, client_address = server_socket.accept()
	print(f"Connection from {client_address}")
	
	try:
		data = connection.recv(1024) # Receive data from the client
		print(f"Received data from client {data.decode()}")

# Create a unix domain socket
client_socket = socket.socket(socket.AF_UNIX, socket.SOCK_STREAM)
try: 
	client_socket.connect(socket_file)
	# Send the data to the server
	message = "Hello from the client!"
	client_socket.sendall(message.encode()) 
```
### Signals
Signals are used to notify a process that a particular event has occurred. For each process there are default signal handlers. A process may override them if it chooses to. 
```python
def signal_handler(sig, frame):
	print('Signal handler called with signal', sig)
	sys.exit(0)

# Register the signal handler
signal.signal(signal.SIGINT, signal_handler)

print("Press Ctrl+C to trigger the signal handler.");

while True:
	sleep(1)
```

- `SIGABRT` - Abort function is called
- `SIGFPE` - Floating point exception
- `SIGINT` - Interrupt, interactive attention request sent to the program, most common from `^c`
- `SIGSEGV` - Segment Fault
- `SIGTERM` - Termination Request
- `SIGKILL` - Force Kill
#### Sending signals to a process
In C use the `kill(pid, signal)` function.  In the Linux shell you can use `kill signal pid` to send a signal to a process. For example, `kill -SIGTERM 1432` sends a termination signal to a process with PID 1432 The process can ignore the signals, except the kill signal. 

### Memory Sharing 
Shared memory using memory-mapped I/O
![[Pasted image 20240320153707.png | center | 300]]

The `mmap` allows mapping of memory. It was originally designed to map a disk file into memory so that you can access its content directly via pointers instead of file read/write.
![[Pasted image 20240320153903.png | center | 300]]

```c
int fd = open("example.txt", O_RDONLY);

// Get the sze of the file
struct stat fileStat;
fstat(fd, &fileStat);

// Map the file into memory
char *fileContent = mmap(NULL, fileStat.st_size, PROT_READ, MAP_PRIVATE, fd, 0);
```

`mmap` vs file operations - `mmap` has several advantages over file operations. 
- `mmap` allows random access, while `fread`/`fwrite` is good for sequential access
- `mmap` can load super large files. 

MMAP IPC let you share memory between two processes:
- Create a shared memory object file using `shm_open()`
- Map the same object file to both processes using `mmap()`

```c
int shm_fd = shm_open(name, O_CREATE | O_RDWR, 0666);
mmap(0, SIZE, PROT_READ | PROT_WRITE, MAP_SHARED, shm_fd, 0);
```

# Threads
A process is an instance of a program that runs in its own memory space. A thread however is a basic unit of executions within a process with their own registers and stacks while sharing the process resources (Code, data, files and memory). 

A process can have multiple threads and allow different aspect of a process to run concurrently. 
![[Pasted image 20240320155024.png | center | 200]]
Because the threads are sharing the same memory we don't need ICP to communicate, instead we can just do in memory communication
### Thread Applications
Ideally, we should use an amount of threads equal to the number of CPU cores. This ensures efficient use of available resources while minimising context switching overhead. 

An Example of an application of threading is the MYSQL[[01 Intro to Databases#Database systems | DBMS]], here each client connection is handled by a separate thread. This allows those clients to execute queries and transactions concurrently without impacting each other. 

Another application of threading is multithread sorting. This is using a [[4. Sorting#Merge Sort |divide and conquer]] sorting technique where the array is split into multiple smaller arrays which are sorted in different threads before being merged back into one array.  

Every GUI application has a main thread to manage the user interface (UI) and respond to user interactions. Doing heavy computation int he main thread will freeze the application.

This is known as "application not responding (ANR)" causes some commonly seen error messages in prompts asking if you want to wait or end the process. 

A well written GUI application will have at least two threads, one that is responsible for UI elements and interaction. As well as other "Job" threads to do all heavy computation. This adds responsiveness to GUI applications by allowing the UI to be rendered/interacted with during heavy computation
### Thread Implementations
There are two types of implementations for threads

| System level threads                                                                                                                              | User-level Threads                                                                                                                                   |
| ------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| - These are constructed and controlled by system calls. <br>- The kernel knows the state of each thread. <br>- You do not have much control here. | - Threads are managed by the runtime or library. Not the kernel<br>- This comes with a lower overhead. <br>- The kernel is not involved much at all. |

#### System level threads
There is a "very famous" library called POSIX Threads or `pthreads` that is a standardised API used for creating and managing threads in Unix-like operating systems. `pthreads` provides a portable and efficient way to implement concurrent programming.

To create a new system level thread you use `pthread_create`
```c
int pthread_create(pthread_t *thread, const pthread_attr_t *attr, void *(*start_routine)(void *), void *arg);
```
- `pthread_t`: Thread ID variable. 
- `pthread_attr_t`: Thread attributes (optional, use `NULL` for default). 
- `start_routine`: Pointer to the function executed by the thread. 
- `arg`: Argument passed to the `start_routine`.

The other main function that POSIX Threads provided is `pthread_join` which is used for waiting and synchronising with the termination of a thread.
```c
int pthread_join(pthread_t thread, void **retval);
```
- `thread`: Thread ID to wait for. 
- `retval`: Pointer to a variable where the exit status of the joined thread is stored.

![[Pasted image 20240320164458.png | center | 300]]

#### User level (Green) Threads
Managed by runtime or library, not the kernel. Lower overhead as no user-kernel switch.  The kernel provides one system level thread to the process. The process "splits" this thread for multiple tasks.  This concept exists in may high-level languages like Python, Java and JS.

![[Pasted image 20240320165701.png | center | 200]]

The reasoning for using green threads over system threads is to do with the different types of CPU task load patterns.  CPU Bound and IO Bound, in IO bounded tasks, most of the time is spent waiting for IO tasks to happen. With green threads, we can increase the utilisation of CPU time by filling in gaps during long IO operations.

![[Pasted image 20240320165958.png | center | 300]]

##### Why Green Threads?
The C10k problem: To handle 10K network connections at the same time. 
- Too costly to create 10K system-level threads. 
- Bookkeeping threads or processes in kernel has high overhead. 
- For example, 1MB for each thread, then we need 10 GBs.

Instead we can use green threads:
- Idea: A significant portion of the time is spent waiting for data to be sent or received over the network (i.e., doing nothing) 
- Green threads have a very low bookkeeping overhead (no user-to-kernel mode switch)
##### Python Implementation
To start a new green thread, use `gevent.spawn()`, to join threads use `gevent.join()`. Thread scheduling and switching occurs when you are calling I/O, networking, and sleep function. 

```python
import gevent

def task1():
	for i in range(5):
		print("Task 1 working")
		gevent.sleep(1)

def task2():
	for i in range(5):
		print("Task 2 working")
		gevent.sleep(1)

# Create green threads
greenlet1 = gevent.spawn(task1)
greenlet2 = gevent.spawn(task2)
gevent.joinall([greenlet1, greenlet2]) # Wait for green threads to finish
```

A green thread need to voluntarily stop its current job from time to time and let other threads execute (cooperative tasking). Yield its CPU by calling IO, networking and sleep functions. 

A  will hog the CPU time and will not give other tasks a chance to run...
![[Pasted image 20240320171805.png | center | 300]]
### Asynchronous IO
A more modern paradigm to handle IO bound tasks. Conceptually similar to green threads. The idea is that you yield to other tasks when the IO operation is underway. 

Coroutines are computer program components that allow execution to be suspended and resumed, generalising subroutines for cooperative multitasking
```python
import asyncio

async def say_after(delay, what):
	await asyncio.sleep(delay)
	print(what)

async def main
	print("starting...")
	await say_after(1, "Hello")
	await say_after(2, "World")

# Time: Output, 0s: Starting, 1s: Hello, 3s: World
```


# Concurrent Programming
This is a program that arises from having multiple threads/processes running at the same time at their own pace. 

When they try and access the same data (structures/objects/devices) at the same time problems can occur from some resources (like a printer) only being able to be safely used by one thread at a time.  

A race condition is any situation where the order of execution on processes/threads can cause different results.  Non-deterministic phenomenon is due to CPU scheduling. 
### Critical Sections
A sequence of code in which we only want one thread to be active at a time. If there is already a thread in the section, then we wait for the thread to leave first before executing. 

Add $1 to account
```
Enter_Critical_Section()
Read Balance
Balance++
Write Balance
Leave_Critical_Section()
```
#### Implementation
We need a way of locking threads out of critical sections. 

To being with , we use a Boolean variable to store whether the section is occupied. Initially its value is false.

**Wrong** critical section implementation
```
Enter_Critical_Section: 
	1. while locked: do nothing. 
	2. locked = true 

Leave_Critical_Section: 
	1. locked = false
```

This is bad as you could get a situation where two processes could enter the critical section at the same time due to CPU scheduling forcing a process to yield between the two instructions. 

Here testing the variable and setting the variable are two separate steps. If the process yields between these two steps then you end up with both processes entering the "locked" section.
#### Atomic Instructions
To achieve the desired behaviour, we need an indivisible instruction to do both
- `while locked: do nothing`
- `locked = true`
We call it an atomic instruction, as there is nothing in between. It requires hardware support.
##### Test and set
One instruction is `testAndSet(locked)`, which does the following two things together:
1. Returns the current value of the `lockVariable`
2. Sets the `lockVariable` to true only if it is false. 
```
Enter_Critical_Section: While testAndSet(locked): do nothing 
Leave_Critical_Section: locked = false
```
##### Compare and Swap (CAS)
Another instruction is `CAS`. There are 3 parameters for a `CAS` operation: `CAS(V, A, B)` 
1. A memory location `V` where value has to be replaced 
2. Old value `A` 
3. New value `B` which should be written over V 

`V` should have the value `A`; if it does, put `B` there. i.e., the swap takes place only under this condition.
```
Enter: While !CAS(lockedVariable, false, true): do nothing 
Leave: lockedVariable = false
```

CAS in x86 CPUs
```c
int value = 42;
int expected = 42;
int new_value = 99;

// Perform CAS operation using inline assembly
asm volatile (
"lock cmpxchg %2, %1" // CAS Operation
: "=a" (value)        // Output operand (current value)
: "m" (expected),     // Input operand (memory location)
"r" (new_value)       // Input operand (new value)
: "memory", "cc"      // Clobbered registers
)
```
This is not recomended 

```c
bool __atomic_compare_exchange(size_t size, void *ptr, void *expected, void *desired, int success_order, int failure_order)
```
#### Spin Lock
Have the CPU do busy waiting (looping) to get the lock. This is called spin-lock. 

This used often as it is easy to implement and generally the time required to wait is minimal so it is acceptable to do this. 
![[Pasted image 20240320175053.png | center | 200]]
```c
#include <linux/spinlock.h>
static DEFINE_SPINLOCK(my_spinlock); // Define a spinlock
spin_lock_irqsave(&my_spinlock, flags); // Aquire the spinlock
spin_unlock_irqrestore(&my_spinlock, flags); // Unlock
```
#### Atomic Variables
For if you just want to update a variable with a basic type atomically.

Use type specifier `_Atomic` - `Atomic_load`, `Atomic_store`, `Atomic_fetch_add`
```c
int main() {
	_Atomic int atomicVar = 0; 	// Declare an atomic integer varaible
	atomic_fetch_add(&_AtomicVar, 1); 	// Perform atomic increment
	int value = atomic_load(&_AtomicVar); 	// Perform atomic load	
	return 0;
}
```
#### Mutex
Mutex lock: Boolean variable indicating if lock is available or not. A wrapper for low-level hardware instructions.

You can declare a mutex by `pthread_mutex_t mutex;` Then enter critical section by calling `pthread_mutex_lock(&mutex)`. When you try to lock a locked mutex from a different thread, the second thread will wait for the first thread to unlock before continuing. 

![[Pasted image 20240320181720.png | center | 300]]

```c
void* threadFunction(void* arg) {
	for(int i = 0; i < 5; i++) {
		// Lock the mutex before accessing the shared data
		pthread_mutex_lock(&mutex);
		
		// Critical Section
		int localData = sharedData;
		localData++;
		sharedData = localData;
		
		// Unlock the mutex after accessing the shared data
		pthread_mutex_unlock(&mutex);
		
		//Simulate somenon-critical work
		usleep(100000);
	}
	return NULL;
}
```

Linux kernel mutex:
```c
#include <linux/mutex.h>
static DEFINE_MUTEX(my_mutex); 
mutex_lock(&my_mutex); 
mutex_unlock(&my_mutex);
```
#### Semaphore
An advanced version of Mutex Lock. The semaphore variable is an integer now. For example, you have three printers, so you allow 3 threads to use them at the same time. 

To get a resource the thread calls `sem_wait()` on the semaphore. It waits for the semaphore to become positive and then decreases the variable by `1` (`s -= 1`). 

To return the resource, the thread calls `seg_signal()`. It increases the semaphore by 1 (`s+=1`).

`sem_init(&semaphore, false, 1);` Creates a semaphore with 1 thread that can access. 

```c
void* threadFunction(void* arg) {
	int thread_id = *(int*)arg;
	sem_wait(&semaphore); // Wait (decrement) the semaphore
	sharedVaraible++; // Critical section
	sem_post(&semaphore); // Signal (increment) the semaphore
	pthread_exit(null);
}
```

#### Read-Writer Lock
The Reader/Writer Problem is where there are a number of thread, and to preserve the integrity of the shared data, we need to ensure:
- If no writer is active there can be multiple readers
- If a writer is active there must be no other active readers/writers. 
- 
`pthread_rwlock_init` Initialises a `rwlock`, `pthread_rwlock` acquires a read lock. 

```c
void* reader(void* arg) {
	while(1) {
		pthread_rwlock_rdlock(&rwlock);  // Acquire read lock
		printf("Reader %ld: Shared data = %d\n", (long)arg, sharedData);
		pthread_rwlock_unlock(&rwlock); // Release read lock
		usleep(500000);
	}
	return NULL;
}
```

`pthread_wrlock` acquires writer lock
```c
void* writer(void* arg) {
	while(1) {
		pthread_rwlock_wrlock(&rwlock);  // Acquire write lock
		sharedData++;
		printf("writer %ld: Set shared data = %d\n", (long)arg, sharedData);
		pthread_rwlock_unlock(&rwlock); // Release write lock
		usleep(1000000);
	}
	return NULL;
}
```
Here `pthread` does the reader writer optimisations, based on the criteria above and the active write / read lock(s). 
#### Monitors
These are concurrency Construct in high-level languages. Java provides synchronised keywork to allow the updating of values concurrently by only allowing one thread to access at a time. The section described by `synchronized` becomes a critical section. 
```java
public class SharedResource {
	private int counter = 0;
	
	// Method demonstrating synchronised block
	public void incrementCounter() {
		synchronized(this) { // Monitor lock on "this" object
			counter++;
		}
	}
	
	// Method demonstrating synchronised block
	public synchronized void decrementCounter() {
		counter--;
	}
}
```
#### Lock-Free Algorithm
An algorithm that guarantees that at least one thread can make certain progress within a finite number of steps even in the presence of contention and delays. 

```python
def add_to_balance(increase);
	previous_amount = balance
	while(!cas(&balance, previous_amount, previous_amount + increase)):
		previous_amount = balance
```
If the `cas` returns `false`, It means someone toped up the account when you trying to top up the account. Just get the new balance, and do the `CAS` again.
#### Lock-Free Data structures
Many high-level languages provide lock-free data structures such as [[7. Data structures#Java Collections|stacks, queues, and sets]]. Internally, they may use compare and swap hardware instructions

### Producer and Consumer problem
![[Pasted image 20240320184717.png | center | 150]]
When you have a producer and a consumer both working on one buffer, you can have problems if the producer an consumer are operating at different speeds. Producer can't add if the buffer is full and the consumer cannot consume if the buffer is empty. 

This can be solved with semaphores, Setup two semaphores:
- One for number of empty slots, 
- One for number of occupied slots. 

Consumer decreases number of occupied slots by 1 (`sem_wait`) and increases number of empty slots by 1 (`sem_signal`). 

Producer decreases number of empty slots by 1 and increases number of occupied slots by 1.
### Tips for writing concurrent code
1. Don't do it, if you can avoid it. 
2. If you must do it, don't share data across threads 
3. If you must share data across threads, don't share mutable data 
4. If you must share mutable data across threads, synchronise access to that data 
5. Use high-level languages designed for concurrent computation, for example, `Go`

### Go Programming Language 
Go uses channels to connect threads for data sharing. 
- `ch <- data // Send data to channel ch`. 
- `data:= <-ch // Receive from ch, and assign value to data.` 

Internally, a channel generates data copies, so no concurrency issues

# Deadlock
A Deadlock occurs when a processes enters a waiting state because a requested system resource is held by another waiting process. Which in turn is waiting for another resource held by another waiting processes. 

A circle of processes each holding a resource wanted by another process in the circle.

![[Pasted image 20240408233528.png | center | 200]]

For a deadlock to occur, there are four conditions that need to happen simultaneously:
1. **Hold and wait** - A process can hold a resource while requesting another
2. **Mutual exclusion** - Only one thread at a time can use a resource. Resources cannot be shared. 
3. **No Pre-emption** - Only the owner can release the resource
4. **Circular wait** - There is a circular list of processes each wanting a resource owned by another in the list. 

A resource graph is a data structure that represents resources and processes as vertices in a graph. 
- Resources are represented by rectangles 
- Processes are represented by circles
- Allocations and requests are directional edges

A loop in the graph indicates a potential deadlock, This is a necessary but not sufficient requirement. 
![[Pasted image 20240408234134.png | center | 200]]
### Deadlock prevention
To prevent a deadlock, you must invalidate one of the four necessary conditions. 

**Mutual Exclusion:** Make the resources sharable, Don't put locks on sharable resources (e.g. read only files)
**Hold and Wait** Only do One resource at a time. Make sure that when a thread requests a resources, it does not hold any other resource. 
**No Pre-Emption** Allow a resource to be forcibly removed from a process. This is very difficult to implement as it causes processes to break. One example would be restarting your computer if your OS enters deadlock. This kills all the processes to removes all waits ?
**Circular wait** Impose a ==total ordering== of all resource types. Each thread requests resources in an increasing order of enumeration. If you have already obtained resource 3 then you cannot request resource 1. 

A **Total ordering** means that every element of the set is comparable with every other element in the set. One example of this are natural number, $1 \leq 2, 2\leq 100$. In a **partial ordering** however not every pair of elements is comparable. With vectors, we may define $[1, 2] < [3, 4]$ but what about $[1, 4]$ and $[2, 3]$

There are two ways to *recover from deadlock*. The first way is just to **kill the processes** involved. Force it to give up the resources. You can kill all related processes but this might cause large disruptions. Alternatively you can just kill the ==processes of least importance== but this might result in the deadlock occurring again immediately. 

Another method is using **checkpoints & rollbacks**. Roll back to a safe state to release the resource.  Here the system needs to maintain checkpoints *Starvation* is where the processes repeatedly rolls back to the same point and doesn't achieve anything. 

### Deadlock avoidance algorithm
Before granting resources, we check if deadlock could occur if we allocate this resource to this process.  A conservative solution would be to assume a process may need all relevant resources, the drawback of this is that a processes might et stopped from getting a resource even though it is available. 
#### Banker Algorithm
`P1` (`1 2 2 1 of 3 3 2 2`) needs `2 1 0 1` (`2A`, `1B` and `1D`)more resources. This is available so we grant it, available resources becomes
```
3 1 1 2  -  2 1 0 1 = 1 0 1 1
```

After granting the resource, we assume `P1` will finish and thus release the resource
```
1 0 1 1  +  3 3 2 2 = 4 3 3 3
```

We follow a similar processes for `P2` and after it finishes we have `5 3 6 6`
Same for `P3` then all resources are available with no deadlock. If `P2` was asking for heaps of resources, we would not be able to grant it and there would be a deadlock in the middle.?
### Livelock
Where processes keep running but no actual progress is made.  An example of this is when two people meet face to face in a corridor. Both moving side to side in sync so neither can get past. 

# CPU Scheduling
### Process State 
As a process executes it changes between 5 different states
- `New` The process is being created
- `Ready` The process is waiting to be assigned toa  processor
- `Running` Instructions are being executed
- `Waiting` The process is waiting for some event to occur
- `Terminated` The process has finished execution
![[Pasted image 20240409140130.png | center | 300]]
### Runnable
A runnable process means it is either running or ready to run, We usually have many runnable processes but a CPU Can only run one process at a time. The kernel decides which runnable to execute on the core at any given time. By jogging between different processes very fast, we can achieve multi-tasking. 
### Multitasking Models
**Corporative Multitasking** is implemented in green threads. This requires a process to be considerate and voluntarily yield its right to run from time to time. A selfish process can make the computer unresponsive.  This is normally only implemented in old operating systems because uncooperative processes lead to too many issues. 
 
In **Pre-emptive Multitasking** each process is assigned a specific amount of CPU time. Upon reaching the time limit, the CPU halts the current process and works on other processes. Every runnable will be executed without having to worry about "selfish" processes
### Context Switch
The change from one running process to another on the same processor is usually referred to as a context switch. A context contains registers, memory, file, etc. Context is stored or restored when switching process, the context of a process is stored in the PCB.
![[Pasted image 20240409141426.png | center | 300]]
### Waiting
It is very rare for a process to have all the resources they need when they start. Waiting processes are suspended and put into a waiting queue. When the resources become available, the process is removed from the waiting queue and becomes runnable again. 
![[Pasted image 20240409141727.png | center | 240]]
In a terminal shell, you can use `Ctrl + Z` to temporarily stop a processes (Set it to the waiting state) . This allows other processes to run to completion more rapidly. You can use `jobs` to show all stopped processes and `fg` to resume a process
### Termination
Process executes last statement and then asks the operating system to delete it using the `exit()` system call. Or simply return from the main function in C, an `exit()` system call will be made implicitly.  Parent process calls `wait()` to wait for the child to terminate
### Scheduling
A schedular determines the order in which processes are executed from the ready queue. It tries to acheive maximum CPU utilidation. In real life, most programs feature a *CPU - I/O Burst Cycle*. This is wear CPU Execution is followed by I/O Waiting However, the CPU Burst is generally very small and the IO is quite slow. For example a 1ms CPI burst is followed by 100ms of I/O Wait. The CPU Utilisation is less than 1%
![[Pasted image 20240409142557.png | center | 220]]
The CPU scheduler selects from among the processes in the ready queue (i.e., ready to do CPU burst) and allocates a CPU core.  CPU scheduling happens periodically. CPU scheduling decisions may take place when a process.
1. Switches from running to waiting state 
2. Switches from running to ready state 
3. Switches from waiting to ready 
4. Terminates
#### CPU Dispatcher
The dispatcher module gives control of the CPU to the process selected by the CPU scheduler.
1. Switching context 
2. Switching to user mode 
3. Jumping to the proper location in the user program to resume that program 
Dispatcher Latency is time it takes for the dispatcher to stop one process and start another running
#### Scheduling policies
To evaluate a scheduling policy you should look at:
- **CPU utilisation** - keep the CPU as busy as possible 
- **Waiting time** - amount of time it takes from when a request was submitted until the first response is produced. 
- **Throughput** - # of processes that complete their execution per time unit 
- **Turnaround time** - amount of time to execute a particular process
##### First Come First Served (FCFS)
1. Non pre-emptive
2. Easy to determine which process should run next. 
The waiting time for a process is the duration from the moment it starts queuing until it begins processing. **Convoy Effect**: a lengthy process arrives first and takes a substantial amount of time to complete, causing all the shorter processes behind it to experience prolonged waiting times.
##### Shortest Job First (SJF)
SJF in theory can gives minimum average waiting time. Unfortunately, we usually don’t know which process has the shortest CPU burst.
##### Pre-emptive SJF
Whenever a new process arrives in the ready queue, with a shorter burst time than the remaining burst time of the running process then the process is pre-empted
![[Pasted image 20240409150007.png | Center | 350]]

How much time the process stay in the ready queue without doing anything. 
$P1 = 11 - 2$  $P2 = 5 - 4$  $P3 = 4 - 4$  $P4 = 7 - 5$  Average waiting time $= (9 + 1 + 0 +2)/4 = 3$

Starvation would happen if there is a continuous influx of short processes.  So have the priority of a process increase the longer it waits in the queue
##### Round-Robin
Treat everyone equally, Give everyone a time slice to run. If not finished with the time slice, move to next process.
##### Priority Scheduling
Each process is pre-assigned with a priority. Run in order of priority
**Explicit Priorities**: Set a priority before a process runs. The priority never changes. 
- Starvation: jobs with higher priority keep coming, so lower priority tasks never get executed. 
**Varying Priorities**: A job priority can varying during its lifetime. 
- Starvation prevention: as time progresses, the priority of the process increases.
In Linux/Mac OSX, you can use the `nice` command to start a process with a specific priority. Nice value range from `-20` (highest) to `19` (lowest).
#### Multilevel Queue Scheduling
Different queues may use different scheduling algorithms. Different queues can have different priorities (e.g., always dequeue from the system processes first). A process may switch its queue.
### Multi-core Scheduling
There are many ways to increase the speed of computation, a good one is increasing the number of logical cores. There are two main architectures for creating multi-core CPUs

**SMP** (Symmetric MultiProcessing) is the base of most CPUs. Contains multiple identical processors that share bus memory and I/O devices. All have the same access latency. This doesn't scale well as only one core can access memory at a time. 
**Simplicity**: SMP systems are relatively simple to design and implement.
**Scalability**: The shared bus architecture can limit scalability as the number of processors grows.
![[Pasted image 20240507091009.png | center | 300]]In NUMA (Non-uniform memory access) CPUs, each CPU has its own private memory, thus no need for arbitration and waiting. Some memory regions are faster to access.
**Simplicity**: NUMA architectures can be more complex to design and maintain.
**Scalability**: NUMA scales better as the number of processors increases.
#### Scheduling
For a SMP CPU, two possible ways to schedule tasks. All runnable tasks may be in a single queue or each processor may have its own private queue.
![[Pasted image 20240507091229.png | center | 200]]
NUMA scheduling is a generally challenging problem, the main goal is to improve memory locality and reduce access latency. 
### Distributed Computing
Multiple computers work together to solve a common problem.Each computer itself can have their own process architectures. Computers communicate with each other over network protocols. These are often not fast or even reliable. 

Scheduling becomes even harder with increased state inconsistency and deadlock. The two general problem applies here, and has been proven to be unresolvable. In real life we can design a robust communication protocol to reduce the chances of state inconsistency. 
### Distributed Deadlock
Each node keeps track of the resource allocation graph to do with its local resources. A central node is used to gather and union the resource graphs from different nodes. The resource graphs can be outdated sometimes. It is an approximation algorithm. 
![[Pasted image 20240507092030.png | center | 200]]
To avoid distributed deadlock:
1. Resource total ordering.
2. Banker Algorithm.
	- All requests checked by a Banker process. becomes a single point of failure
3. Process total ordering.

**Process total ordering:** Prevent this circle by killing/restarting younger processes\
**Timestamp Prevention algorithm**  *Wait-Die*: Process A requests a resource held by process B. If process A is older (smaller timestamp) than process B, it waits for the resource. Otherwise process A dies/restarts, process B (the older) continues. *Would-Wait*: Process A requests a resource held by process B If process A is older (smaller timestamp) than process B, it takes the recourse and restarts B. Otherwise process A just waits. Processes keep their timestamp even they restart
![[Pasted image 20240507092431.png | Center |200]]

### Real time scheduling
Used in realtime applications. In **Hard** real times systems, task must be serviced by its deadline. 
**Soft** real time systems: Critical real time tasks have the highest priority, but no guarantee on deadline. 
**Event latency** - the amount of time that elapses from when an event occurs to when it is serviced.
![[Pasted image 20240507092804.png | center | 200]]
#### Submitting real time tasks
When processes are submitted, they can indicate their CPU requirements., The scheduler may reject the process if the requirement cannot be met.
#### Periodic Real time process
A process requires the CPU at constant intervals
- Used for polling, monitoring and sampling
- Example: sample air quality at a frequency of 100 HZ

**Allocation Requirement** ($c$, $p$, $d$)
- $c$ - computation time (obtained through analysis, measurement or simulation)
- $p$ - period time (determined by application)
- $d$ - deadline
such that $c < d < p$
![[Pasted image 20240507093058.png | center | 300]]
#### Sporadic Process
Event driven – some external signal or change
For example: a user pushes a button, a response must be received within one second.

Allocation requirement $(c, p, d)$ still applies. $p$ is the minimum time between event. 
- If $p = 0$, events can happen at any time

#### Cyclic Executives
A scheduling method for periodic processes.
- Sporadic processes can be converted into equivalent periodic processes.
**Major schedule:** cyclic allocation of processor blocks which satisfies all deadlines and period.
**Minor cycle (or frame):** major schedules are divided into equal size frames.

![[Pasted image 20240507093253.png | center | 300]]

$A = (1, 10, 10), B = (3, 10, 10), C = (2, 20, 20) D = (8, 20, 20)$

Major cycle time is LCM of periods, which is $20$. Frame time is GCD of periods, which is 10
![[Pasted image 20240507093333.png | center | 250]]


# Drivers
# File Systems
We need some way of storing information; Independently from a running program, So it can be used at a later time, Permanently (or an approximation to it), Non-volatile storage (power can be turned off), So it can be shared with other program (process) or users.

There are an infinite variety of ways of data is to be stored
- The more information the OS knows about the data the more it can facilitate use of the data.
- e.g. Documents, Media, Executable files, System files.

Naming the data
- The data needs to be stored and retrieved easily. We need a way to name the data.
- We must then be able to locate the data using its name. (Directory ?)

Textbook definition of a **file**: A named collection of related information that is recorded on secondary storage. (Not always true).  In some systems “files” are not always files e.g. In UNIX devices are “files” and so are some operating system information structures (e.g. /proc in Linux)
### File attributes
- **Name** – The symbolic file name is the only information kept in human readable form.
	- Many systems use a byte to indicate the file name length and so are limited to 255 characters. There are usually limitations on the characters you can use in filenames.
- **Identifier** – unique tag (number) identifies file within file system
-  **Type** – needed for systems that support different types
- **Location** – where is the file stored, some pointer to the device (or server) and the positions on the device
- **Size** - current file size (either in bytes, blocks, number of records etc)
- **Owner information** – usually the owner can do anything to a file
- **Protection** – controls who can do reading, writing, executing
- **Time, date, and user identification** – data for protection, security, and usage monitoring
And more like: Information about files (metadata) are kept in the directory structure. Both the directory structure and the files reside on disk. Many variations, including extended file attributes such as file checksum
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
### File Structure
File Type – represents the internal structure of a file.
- .exe files have an OS specific structure – information for the loader about necessary libraries and where different parts should be loaded and where the first instruction is.
- All operating systems “know” about executable binary files.
- System-supporting multiple file structures makes OS large and cumbersome (code for all supported structures)
- UNIX – files as a sequence of bytes; no interpretation of these bits is made by the OS.
### File system operations
A file can be considered as an abstract data type that has data and accompanying operations. On most systems these commands need security authorisation to perform and they work on the file as a whole..
#### Create
Need to specify the name and possibly the file type. Providing the size of the file helps keep storage contiguous but is usually unnecessary. Creation needs to write a structure to the associated device. Some systems allow transitionary files to not be recorded permanently in secondary storage i.e. they only exist in memory.
#### Delete
Remove the file. Return the space used by the file. If the deletion was a mistake, the file might be in in a “going away” area from which it can be retrieved.

When data is deleted on an SSD with TRIM (unallocated), it is lost. TRIM clears space by storing content to flash, erasing the cell then rewriting a sorted/optimised version to that cell (or sometimes a new one?).
#### Open
Several pieces of data are needed to manage open files:
- Open-file table: tracks open files
- File pointer: pointer to last read/write location, per process that has the file open
- File-open count: counter of number of times a file is open
- Disk location of the file: cache of data access information
- Access rights: per-process access mode information
#### Others
**Close** Decreases the open count, When the open count reaches zero, the file is no longer in use - allow removal of data from open-file table when last processes closes it. `create()` and `delete()` are system calls that only work with closed files.
**Moving** a file can be performed in different ways depending on the before and after locations. If both locations are on the same device the data doesn’t have to be copied and then the original deleted. Instead change information about the file (metadata).
Most file systems preserve attributes (including last modified times) when a **copy** is made. In some situations we can use *copy on write* rather than making complete copies. The file information needs to point to the original data.
The user can **change** some **attributes**, others should be secured by the OS.
#### Read
These operations work on the file contents. We need to know where the information is on the device. Must specify what data to read, how much, and where to put it.

| Sequential access                                                                                                                                                                                                                                                            | Direct (or random) access                                                                                                                                                                                                                                                                                                                                                  |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| - Data is retrieved in the same order it is stored<br>- There is a current position pointer somewhere<br>- May be stored within the using program<br>- May be stored within the file system (but separately for each process)<br>- Has ramifications for distributed systems | - Provides immediate access to large amounts of information.<br>- The read specifies exactly where it wants to get the data from.<br>- It could be a byte offset or a record number (an index relative to the beginning of the file)<br>- Some file systems let you specify record length when you create a file. Others leave all such control up to individual programs. |

![[Pasted image 20240417200404.png | center | 300]]
#### Write 
Very similar to read but commonly requires the allocation of extra space. Direct (random) access writes can create holes. The program “seeks” to the new position.
If this is outside the bounds of the file, then we have two choices.
- Allocate all of the intervening space and fill it with some null value
- Mark the intervening space in the directory as not allocated – this is known as a sparse file

If a process tries to read from the empty space of a sparse file, it gets the null value for the record. If a process writes to the empty space, then real blocks are allocated and written to.
### Design Decisions
Files need to contain vastly different types of information. Some of this information is tightly structured with lines, records etc. At the bottom level the file system is working with discrete structures (sectors, pages and blocks) of a definite size. The most common solution is to treat files as a stream of bytes and any other structure is provided by the programs using the files. Some operating systems provide more facilities than others for dealing with a variety of file types.
### Representing files on disk
Disks deal with constant size blocks (say 4KB). All disk I/O is performed in units of one block (physical record). All files and information about files must be stored in these blocks (usually accessible via a logical block number / logical record). UNIX treats files to be simple stream of bytes - logical record size is 1 byte. We need to know what we want our file system to look like to the users in terms of its structure.We also need to know how we can place the required information on the disk devices
### Single-level directory
Simple, small systems did this, especially with small disk devices (floppies), The directory entry has a pointer to the location of the actual file. 
Disadvantages:
- Finding files as the number of files grows becomes difficult.
- To be workable it requires very long filenames.
### Two Level Directory
The top level is the master file directory contains files for each user/
![[Pasted image 20240422191820.png | center | 300]]

Creating a user file directory is usually only allowed for administrators. When people log in, they are placed within their own directories. Any files mentioned are in that directory. If a user has the appropriate permissions, they are able to access other users' files using the full path name. 

System files can be placed in a single user file that every user has permissions to access. 
#### Tree Structure
Tree structured directories allows as many directories as required. This facilitates the organisation of collections of files. Every file in the system has a unique path name, (absolute or relative), Directories are special files. 
### Sharing files - Links
We very often want the same data to be accessible from different places across a OS. e.g. two people might be working on a project and they both want the project to be available in their own local directories. 

 **Multiple directory entries:** We could make copy of the file record (or directory entry) information (not the data). There is a problem with consistency and data integrity. 
**Soft links:** There can be one true file entry in one directory. Other entries have some reference to this entry. UNIX **symbolic links** and Windows shortcuts. 
**Hard links:** There can be a separate table with file information. Then all directory entries for the same file just point to the corresponding information in this table. UNIX and NTFS hard links
![[Pasted image 20240422192812.png | center | 300]]
> Both UNIX and NFTS can only make links between files on the same volume. 


| Unix                                                                                                                                                                                   | NFTS                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| directory entry stores a pointer to the file's `inode` which holds the real information about the file.                                                                                | Each directory entry holds copies of most file attributes plus a pointer to the file’s Master File Table (MFT) file record, NTFS updates the properties of a hard link only when a user accesses the original file by using the hard link. Hard links do not have security descriptors; instead, the security descriptor belongs to the original file to which the hard link points.                                                                           |
| the `ln -s` switch creates a symbolic (soft) link.  The file called “f3”  a text file with the contents “f1”.<br> If the original is moved, you get unable to open errors for example. | NFTS Shortcuts - Using the Context menu in file explorer you can create a shortcut to any file. Windows using attributes like file type, size and time of modification to find renamed shortcuts. <br><br>This is much better under NTFS with the distributed Link Tracking Client service. Keeps a log of changes made to files that have shortcuts. This means that the file can almost always be found<br>- It sometimes works on distributed environments. |

![[Pasted image 20240422193656.png | center | 300]]
##### Mac Aliases
HFS+ (and APFS) has a unique, persistent identifier for each file or folder. 
- An alias stores this identifier with the pathname.
- Originally the identifier was first used to find the file. Only if this failed was the pathname used. Now in order to work more like symbolic links, the pathname is used first. 
- If a file is renamed and a new file with the old name is created, both an alias and a symbolic link will find the same file (the new one). 
- If the pathname does not find the file but the identifier does, then the pathname is updated to the current correct value.

#### Directory Graph Cycles
If we allow directories to appear in multiple places in the file system we can get cycles, We don’t want to fall into infinite loops if we traverse the file system e.g. to search for a file. In UNIX, directories can only be linked with symbolic (soft) links. Algorithms count symbolic links on directories to stop infinite recursion. 
#### Deleting linked files
With hard links, we maintain a count of the number of links. This gets decremented each time one of the links gets deleted. When this finally reaches zero the actual file is deleted. 
## Directory Contents
The file name (actually the last part of the pathname), we scan the directory to see if the file exists within it.

We can have the file attributes stored in the directory entry. (MS-DOS) 
- At a minimum we need a pointer to the file attributes and location information. 
- UNIX keeps the ==file attributes and location information== (pointers to its data blocks on the disk) of each file in a separate structure – the *inode* (Information node or Index node). 
	- The inode also keeps a count of the number of hard links to the file. 
- The inode table is an array of *inodes* stored in one or more places on the disk. 
- So, a UNIX directory isn’t much more than a table of names and corresponding inode numbers

![[Pasted image 20240428222143.png | center | 300 ]]
### NTFS Directory Entries
All file and folder information is stored in the MFT (Master File Table). Each file has at least one file record consisting of the file attributes, size, location etc. 

Folders have an indexed table that lists all the files (and sub-folders) within that folder. Each entry in the folder table includes the file name, a pointer to the file's MFT entry, and some commonly referenced attributes (e.g., created and modified dates, file length). 

So much of the information in the files MFT record is ==duplicated in the directory entry==. This allows the folder table to act as a kind of cache (?)

This explains the hard link behaviour (ability of NTFS to have multiple directory entries (hard links) pointing to the same MFT record) from last lecture. 

### File Control blocks
There are several file tables held in different types of memory and accessed by different entities. In this case the on-disk representation that holds the file information, attributes and other pointers. In UNIX this is the inode. In NTFS this is the MFT file record. In MS-DOS this is the directory entry.

There must be a place on the disk which points to the blocks allocated to each file.  This can be done in many different ways. It depends on how blocks are allocated to files. 
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
![[Pasted image 20240428223330.png | center | 270]]

Extents: Collections of contiguous allocation (NTFS) - When expanding file, if not enough blocks available add a pointer to a new contiguous allocation. 
#### Linked Allocation 
*linked list of disk blocks*, a small amount of space is set aside in each block to point to the next (and sometimes previous as well) block in the file. The directory entry holds a pointer to the first block of the file and probably the last as well for if a pointer is corrupted.

**Pros:** Simple, and allows for dynamic growth, No external Fragmentation
**Cons:**
- Random Access: Direct (random) access to a particular part of a file can be slow. 
- Reliability: Damage to blocks can cause the loss of a large section of the file 
- Space Overhead: A portion of each block is used for the next block's address, reducing block's effective storage space. 
#### MS-DOS & OS2 FAT
The MS-DOS File Allocation Table (FAT) is a collection of linked lists maintained separately from the files. One entry for each block on the disk. A special section of disk holds the table, separate from the actual data it references. 

Each directory entry holds the starting block number of the file. This number is also an index into the FAT. At that index is the block number of the second block. This number is also an index into the FAT.

Over time the number of bits used to index the FAT changed (8 to 12 to 16 to 32) as disks got larger. So, the size of the FAT and the maximum number of allocation units on the disk grew.
![[Pasted image 20240428224659.png | center | 250]]
**Pros/Cons** Accessing FAT data for a file requires less disk accesses than for normal linked access as one block of FAT data might hold information for several blocks in the file. If we have enough memory to hold the entire FAT in memory, we can determine block numbers with no disk access. As the FAT gets larger it is difficult to cache it all and so it becomes common for a single block access to require multiple FAT block reads.
#### Indexed Allocation
A partial way around this is to keep all block numbers in a contiguous table for each file. Each file has its own index block (or "i-block") which is a dedicated block that stores a list of disk block addresses.

![[Pasted image 20240428225113.png | center | 300]]

**Pros:** Good for direct (random) access. Information for each file is kept in one area. 
No external fragmentation. 

**Cons:** If an index block is lost, we have lose access to a whole chunk of the file. Space wasted in the index block for smaller files. File size limited by the number of indices in the index table.

**Multilevel Indxing** A technique that enables file systems to handle large files efficiently. If the file has more blocks than we can reference from the index block, we need to have some way of connecting to more index blocks. 

We can link index blocks together (like the linked allocation of files). Similar pros and cons. 
We can have multi-level indexed allocation. 
- The first level (called the “indirect block”) points to index blocks. 
- The second level points to the file’s data blocks.
 ![[Pasted image 20240428225412.png \|200]]  ![[Pasted image 20240428225449.png\|250]]  
#### Index Blocks (Multi Levels)
If we have blocks of size 8K, and a block address of 4 bytes, as well as a two level system then we can address files of up to 32 gigabytes. The indirect index block points to 2048 index blocks. Each index block points to 2048 actual blocks. So we can have 4194304 actual blocks in a file. 
![[Pasted image 20240502090154.png | center | 140]]
### Unix Index Block Scheme
In order to minimise the number of blocks read to find the actual block (especially with small files) several versions of UNIX don’t use extra indirect blocks until necessary. NTFS goes one step further and stores small files < 1K in the MFT file record itself.
![[Pasted image 20240502090449.png | center | 260]]
### NFTS Extents
NTFS keeps all index information in the MFT. Rather than storing all block numbers it stores extents.
- A cluster is a number of blocks (2, 4, 8, etc), An NTFS volume is seen as an array of clusters.
- An extent is a start cluster number and a length (number of clusters).
![[Pasted image 20240502090938.png | center | 300]]
### Tracking free blocks
Whenever a new block is required, we need to be able to check if there is an empty block available.  There are lots of possible blocks,  a 6TB disk with 4KB blocks would have about 1.5 billion blocks.

Early versions of UNIX maintained a device wide free list of blocks in a stack, where by the next allocated block is the most recently returned one. This has several downfalls:
- You are not able to assign contiguous blocks as the order is going to be semi random
- On the hardware level, the same few blocks are going to be used repetitively. massively reducing the lifespan of certain blocks. 

**Linked allocation**. Link free blocks together like a large empty file using the linked allocation method. Trivial to find the first free block. But it is not efficient if we want several blocks at a time, especially if we want them to be contiguous.
**Bitmaps:** We can maintain a bitmap (or bit vector). Where each bit represents a used or free block (1 represents free). For the example image this takes up about 200 Megabytes of space. (6TB with 4KB blocks) 
**Grouping Blocks:** We can keep a list of start points and lengths (like NTFS extents). Bit map can then be used to track the availability of clusters/extents.

# File Data Structures
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
• Initiate Open Call, Check If File Is Already Open (Consult system wide open file table), Create Process-Specific Entry, Search Directory Structure, Load File-Control Block (FCB), Update Process's Open-File Table, Return File Descriptor or Handle, File I/O Operations

![[Pasted image 20240502092405.png  | 300]] ![[Pasted image 20240502092648.png |300]]
(a) open (b) read

Here we see:
- The on-disk inodes and data blocks
- The in-core copy of the inode, this includes the reference count
- the system wide open file table (file-structure table), this maintains entries for each open file in the entire system.
- the process open file table, just an array of pointers to the file-structure table

When we use fork, the process specific table is copied. 
#### System Call
Most systems require some open call to make the connection between a process and a file.

The open call does several things (not all OSs do all of these): 
- Searches for the file with that name (in the given directory or path)
- Verifies that the process has access rights to use the file in the way specified 
	- We don’t check after this 
	- Can be a security problem, the `TOCTTOU` (time of check to time of use) problem 
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

This has the advantage of batching other writes to that block which improves performance. If a process wants to a this information it is already/still in memory. 

This however risks loosing data if the system crashes or the volatile memory looses power. A daemon process writes data buffers after 30s, metadata after 5. 

`sync()` or `fsync()` command forces buffers to write
### Unix Append
If the file has been opened in append mode `O_APPEND`, then there is a possible [[08 Concurrent Programming#Race Conditions|race condition]]

Before each write, the file position pointer is moved to the length of the file.  
If another write changes the length of the file before this write completes then there is the race condition. 

The file system must guarantee atomicity for the append write operation. There is an append mode (`O_APPEND` flag) for opening a file.

# Other File Systems
### Recovery From failure
What can go wrong?
- Metadata can be inconsistent with actual data
	- Location information, size of file, protection rights, times etc
- Free pointer blocks
- FCB (Inode or MFT Entries) pointers lost
#### Recovery techniques
**Consistency checking** compares data in directory structure with data blocks on the disk. If it finds any inconsistencies it tries to fix them. This is `fsck` on Unix and `chksdk` on windows. It is very slow and might not always succeed. 
**Backup and Restore** Periodic backups to external devices or cloud storage so data can be restored from a known good state
**Journaling** A system that keeps track of changes that will be made in the file system to ensure integrity. For data integrity during normal operation
**Versioning and snapshots** Maintaining historical version or states of files and systems to roll back to when needed. 
### Journaling File Systems
This is used by many modern file systems to ensure data consistency and prove recovery in the event of failures. (NFTS, ZFS, Many Unix systems) Keeps track of changes and tries really hard to make sure we don't lose this information Usually works on file system metadata.

Maintains a log (journal):
- Add log entry for transaction (On disk)
- Can return to the calling process when committed to the log
- Carries out the transaction (Asynchronously)
- Checks transaction (Marks as complete or removes from log)

If something goes wrong at any point it can redo actions of incomplete transactions. 
#### Physical / Logical Journaling
**Physical Journal** All changed data is recorded in the journal first, then written to the file system. This means that the write happens twice which is slower.
**Logical Journal** Only changed metadata like inode tables/directory structures are recorded. This is faster but ==actual data may not be consistent with metadata==. 
### Versioning file systems
Can be useful to retain and access older versions of files. Can recover from mistakes, restore damaged files and compare versions (track changes). Also useful for security purposes (self-securing storage). 

Keep **Complete** copies of all previous versions:
- Very space intensive
- Quick retrieval and no dependencies on previous versions

Keep a **Log of changes**
- Storage-efficient as only differences between versions are recorded 
- Retrieving requires work to reproduce earlier versions

Keep a **tree with all data** 
- Finding any version takes the same amount of time 
- Can be slow for current version if the tree is big
##### Multi version B-Tree
![[Pasted image 20240504200514.png | Center | 500]]
The version ranges (2-?) show which version the leaf is valid for. ? means up to the present.
##### Comparisons
| Log                                                                        | Tree                                                                                    |
| -------------------------------------------------------------------------- | --------------------------------------------------------------------------------------- |
| Compact Storage                                                            | Compact Storage                                                                         |
| Access to the current version is the same as without versioning            | Quick to revert to any previous version.                                                |
| Slow to revert to previous version, especially if there are many versions. | If there are lots of versions, the tree can be big and current version access will slow |
| Can use checkpoints to improve, but adds space requirements                | Can keep seperate copy of current version but adds space requirements                   |
No method works well if the data between versions is completely different.

#### ZFS Snapshots
ZFS uses a copy on write mechanism that ensures that existing data is never overwritten. Data is written to new location and only after a successful write does the system update pointers to the new data. 

![[Pasted image 20240504200925.png | Center | 230]]
#### Pruning 
All conventional versioning systems use pruning to selectively remove versions to conserve space or simplify version management.  There are different heuristics that are used:
- Fixed number of versions 
- Treat some changes as more important than others 
- “observe” user behaviour, e.g., most often accessed 
- The user has to explicitly request a version be held 
Snapshot systems keep versions of files at particular times, only keep versions for a small number of files
### Self Securing Storage
All metadata, directories and critical files (OS files) are kept on a versioning system (as a security measure). 
Any intrusion (that uses files) can be tracked because the intruder cannot erase changes they have made to the system. We need to maintain all versions between checks for intrusion. This is referred to as the detection window.
If the system is unable to keep enough versions, we signal an alarm. Either something has gone wrong, or someone is trying to force a pruning to hide their tracks.
## Distributed File Systems
A Distributed file system (DFS) is a file system whose clients, servers, and storage devices are dispersed among the machines of a distributed system. It should appear to its clients as a conventional, centralised file system. Key distinguishing feature is management of dispersed storage devices. 

A client interface for a file service is formed by a set of primitive file operations and provides many advantages for users:
- Greater storage/scalability, storage of multiple machines can be pooled
- Greater Flexibility, users can log on to any machine and have access to all their files. 
- Reliability:
	- If a site goes does, the file may still be accessible from another location (fault tolerant)
	- Data can be replicated across multiple nodes
	- Load balancing -- moving jobs to more lightly loaded sites. 
	- Workloads can be distributed to lead to quicker response times in user facing applications
### Models
There are two widely-used architectural models, client-server model and cluster-based model
**Challenges include:** Naming and transparency, Remote file access, Caching and cache consistency
#### Client server model
Server(s) store both files and metadata on attached storage
- Clients contact the server to request files 
- Sever responsible for authentication, checking file permissions, and delivering the file
- Changes client makes to file must be propagated back to the server 
Design suffers from single point of failure if server crashes, Server presents a bottleneck for all requests of data and metadata. Could pose problems with scalability and bandwidth.
#### Cluster Based DFS Model
Files are broken into chunks and stored/replicated across multiple data servers. Built to be more fault-tolerant and scalable than client-server DFS. Clients connected to master metadata server and several data servers that hold “chunks” (portions) of files 

Metadata server keeps mapping of which data servers hold chunks of which files as well as hierarchical mapping of directories and files. File chunks replicated n times.
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
Files named by a combination of their host and local names. Guarantees a unique system wide name. Neither location transparent nor location independent. 

Attach remote directories to local directories: Gives the appearance of a coherent directory tree. E.g., NFS. Complex and most difficult-to-maintain. Any remote directory can be attached anywhere on the local directory tree, the resulting hierarchy can be highly unstructured.
- `/shared/data` could be mounted to `/mnt/data` on a local machine

Single global name structures spans all files in the system: Allowing a similar user experience across different client machines. Open AFS `/afs/example.com/projects/report.txt`
### Remote File Access
When a user requests to access a remote file, after the server storing the requested file has been located the data transfer must take place.

**Remove service mechanism** is one approach to transferring files. A request for access is delivered to the server. The server preforms accesses and their results are forwarded back to user.

One of the most common ways of implementing remote service is the RPC paradigm. Analogous to performing a disk access for each access request, reduces the need for large data transfers across the network.

Beneficial when only a small section of a file is required. When there is frequent communication, this can lead to heavy network traffic. 
### Caching
Network traffic can be reduced by retaining recently accessed disk blocks in a cache. This means repeated accesses to the same information can be handled locally.

Files identified with one master copy residing at the server machine, but copies of (parts of) the file are scattered in different caches. `OpenAFS` caches files in large chunks (64 KB) 

Provides faster response times for the user and reduced network traffic. This however raises the issue of keeping the cache copies consistent with the master file

Cached data can either be stored in main memory or on disk. 

Advantages of disk caches:
- More reliable 
- Cached data kept on disk are still there during recovery and don’t need to be fetched again

Advantages of main-memory caches 
- Permit workstations to be diskless
- Data can be accessed more quickly 
- Performance speedup in bigger memories 
- Server caches (used to speed up disk I/O) are in main memory regardless of where user caches are located; using main-memory caches on the user machine permits a single caching mechanism for servers and user.
#### Update Policy
**Write-through:** Write data to disk as soon as they are placed on any cache. Reliable, poor performance 
**Delayed-write** (write-back caching): Modifications written to cache then written to the server later.
- Write accesses completes quickly; some data may be overwritten before written back, and so only final version gets written 
- Poor reliability; unwritten data will be lost whenever a user machine crashes.
**Delayed-write Variation:** Scan cache at regular intervals and flush blocks that have been modified since the last scan. NFS, but any metadata changes are issued synchronously to the server 
**Write-on-close Variation**, Writes data back to the server when the file is closed ( `OpenAFS`) 
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
The server knows: Who has the file open, for what type of access and where it is in the file. 

When the client calls open, it receives an identifier to be used to access the file. 
- Looks very similar to traditional local file access to the client process. 
- Efficient, the needed data may be read ahead by the server. 
- Information about the file is held in memory. 

If the server crashes it is difficult to start again since all the state information is lost. Server has problems with processes which die, needs to occasionally check.
### Remote service - Stateless
Server does not maintain information on the state of the system. It only responds to requests.

Open and close calls don’t send messages to the server. Handled locally except access privileges.

Requesting processor has to pass all the extra information with each read/write 

Server doesn't have to worry about processes stopping. If the server goes down a new one can start handling requests.
### AFS
There are local and shared namespaces. Local namespace contains files that  appear on the local machine, usually machine specific files. Shared name space has location information in volume location database. AFS is **not** stateless.

`/afs` is the root of all shared files. Identical on each client and location transparent even over a WAN (SSI –single systems image). 

Files can be relocated without removing access (except for a brief time) 
- Copy them across. 
- Update the location servers. 
- The original location still handles old requests - shipping them to the new location.
- Remove the originals.
#### Implementation
The Volume Location Database (VLDB) contains the location information and is usually held by several servers. Servers are dedicated; they are not also clients.

Files are grouped into volumes.
- A volume is commonly the files of a particular user or groups of users.
- The volumes are the things that are referenced in the location database. 
- Volumes can be transparently migrated.

Files identified by `volume:vnode_number:uniquifier` 
- `vnode` numbers can be reused, therefore to keep uniqueness there is the "uniquifier" (extra bits added on until unique)

Client machines run a Cache Manager process
- Finds where files are (from the VLDB). 
- Retrieves files from the host and uses caching rather than remote service 
- Files are cached in large chunks (64K) hopefully the whole file 
- Minimises network traffic and is usually more efficient at both client and server ends

Callbacks:
- A call-back in AFS is a promise from the server that the cached version of a file is up to date.
- Before a file is changed the server breaks the call-backs. 
- Then when another process uses its cached version the Cache Manager detects the broken call-back and refreshes its cached data from the server.

![[Pasted image 20240518193741.png | Center | 200]]

### Remote Procedure Calls (RPC)
**Birrell & Nelson 1984** Hide the message passing system so that it looks like a series of procedure calls. Most requests for service wait until the request is fulfilled – semantically just like a procedure call.  Programmer doesn’t have to package and unpackage data in the messages.
![[Pasted image 20240518194318.png | center | 200]]

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
Heterogeneous networks! Data formats in the different machines may be different.  
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

# Memory

All processes need main memory. Instructions and data being worked on are brought into the processor from memory and sent back to memory.
### Memory Spaces
Even in simple systems, we would like a way of protecting OS memory from our program and enabling programs larger than memory to run.

**Split memory** Can protect with a single fence register. Alternatively, if the OS is in ROM its code and constant data is safe from overwriting.

**Overlays** Load needed instructions over the top of ones Not needed any more as memory is not as limited. Some code and data is always maintained in the memory of the process. Other areas can be removed and overlaid with another overlay. This makes programming more complicated as you need multiple fence registers to separate the memory
### Dividing Memory
With relocating loaders we can have multiple programs in memory simultaneously.
We can have separate partitions of memory with contiguous allocation of memory between a base and limit registers. The base and limit registers are loaded for each process. This is not very memory efficient. 

If we change our base and limit system to produce the address by adding the base register (now called a relocation register) to each address, we can make all processes start at address 0. So, no need to translate addresses at load time.
![[Pasted image 20240608140912.png | center | 500]]

### Two different addresses
- The logical (virtual) address coming out of the process, produced by the CPU
- The physical (real) address used on the address bus to memory.

We still have contiguous memory for each process, but a process can now be positioned anywhere in physical memory without its addresses needing to be changed. 

Rather than moving memory around to make big enough spaces for processes we could have more than one section of physical memory accessed by the same process. We need either a number of base-limit registers or a table of the information.

### Smaller Chunks
This technique evolved in two directions.
1. Same sized chunks – pages
2. Variable sized chunks – segments

Both use Memory Management Units (MMUs) in hardware to do the translation between the logical and the physical address. Chunk address is split into two parts to save computation. Simplifies translation in both systems.
#### Address Translation
In a paged system, addresses are split into a page number and a page offset. Some systems allow variable sizes of pages, including very large pages.

![[Pasted image 20240608151456.png | center | 400]]
ARM multi-level indexing system
![[Pasted image 20240608153107.png | center | 400]]
### Frames and Pages
A frame is a page sized chunk of physical memory that can be allocated to a page from a process. A page is a frame sized chunk of logical memory that fits in a frame. It is common to refer to both simply as pages (physical and logical).

Fragmentation
- No external fragmentation between frames.
- Internal fragmentation in any pages that are not completely used.
- Could be an average of ½ a page per process area (or ½ a page per thread, ½ a page for heap space, ½ a page for code, ½ a page for initialized data, etc ).
- Small pages save space but require larger page tables.

In x64 machines it is possible to use page sizes of 1GiB.
### Tables
Each process has its own page table. (CR3 for Intel) And commonly the OS has a frame table with one entry for each frame showing whether it is free or not and which process is currently using it.

### Segments
Rather than constant sized pages we could design our hardware to work with variable sized chunks – these are known as segments. (Not to be confused with variable sized pages.)

**Memory model** How memory appears to be organised to the program (and programmer) is sometimes referred to as the memory model. A segmented memory model is when we look at memory as a group of logical units all with addresses starting at zero. Processes can have lots of segments like functions and global variables etc.

These logical units fit nicely into hardware specified segments where different amounts of memory can be allocated in one chunk. Because a segment is contiguous we get external fragmentation.

Our memory addresses become `<segment name, displacement>`. The translation process is similar to paging, except there is a length associated with each segment. We have a segment descriptor table rather than a page table.

The physical address is the result of an addition rather than a concatenation (like in a paged system.)
#### Allocation Strategies
Segments don't have internal fragmentation – as we only allocate the amount of space we want.
However, there is external fragmentation. 

We have seen the allocation strategies before: First fit, Next Fit, Best Fit, Worst Fit. We can defragment (coalesce) memory if we want to find large enough chunks. Faster than defragmenting disks.


**Amount of fragmentation:** Knuth’s 50% rule states that if there are n segments there are n/2 holes. Each segment is either below another segment or below a hole. We always combine adjacent holes

Each segment is released independently – so in a steady state system the space above each segment will alternate between being used and being free. 50% of the time there will be a hole above each segment. If the average size of a hole is the same as the average size of a segment, we need about ==1/3 of the memory== free to keep this system running.

### Half speed memory
In both paged and segmented memories every logical memory access requires (at least) two memory accesses.  One for the page/segment table entry, and one for the actual data.

It is possible the number of segments may be quite small and there may be registers for them. The MMUs cache recent page table information in a special fast-lookup hardware cache called associative registers or translation look-aside buffers (TLBs)

You can either:
- Find the page number then access the physical memory.
- Fail to find the page number then access the page table Access the physical memory and Update the TLB
#### Average Access times
![[Pasted image 20240608163455.png | center | 450]]
#### TLB Coverage
The coverage (or reach) is the amount of the address space included in the TLB entries. Typical TLB caches hold between 32 and 1024 entries, With 4K pages this is only 4 megabytes of memory. As working sets increase, some processes have a real performance hit, memory wise.

One solution is larger page sizes. This means more internal fragmentation. More IO (in virtual memory systems). Variable page sizes can be used but they need good allocation algorithms to be worthwhile.
### Page Table size
Another problem with page tables is their potential size. With 32bit address space and 4Kbyte pages (offset of 12 bits). There are 20 bits to index into the page table ≈ a million entries (at least 4MB for each process, depends on PTE size)

Most processes do not use all memory in the CPUs logical address space. We would like to limit the page table to values that are valid. Only allocate the parts of the page table we actually need.

Can do this with a page table length register, flag page table entries with a valid bit.
#### Multi Level page tables
Where there are multiple levels to the page table, the number of reads for one access increases. 

Because of this, we need a really good TLB hit ratio, only have to store relevant tables in memory
#### Hashed page table
Use hash function to distribute, on collision make linked list. 
![[Pasted image 20240608164523.png | center | 300]]

#### Inverted Page table
As the number of address bits increases to 64, we need even more levels of page tables.

Another approach is to keep information about the physical pages (or frames) rather than all of the logical pages. This is known as inverted page tables.

Only need one page table for all processes. Each entry needs to refer to the process that is using it and the logical address in that process.

A logical address is `<pid, page number, displacement>`. Have to search the page table for `<pid, page number>`. Use hash table for the page table and rely on TLBs.


### Large Programs
It is very common for programs to be bigger than system memory. This was handled early on by overlays. However, with multiprogramming we can swap entire processes out to disk to provide space for others (and swap back in to run).

The disk is known as backing store and it must be able to hold all memory for all processes.
### Virtual Memory
We can execute programs without the entire program being in memory at once, keep either pages or segments on disk when not needed. The logical (virtual) address space can be larger than the size of our physical memory.

Unused code doesn’t waste physical memory, We have more memory for multiple processes. We don’t need to load the whole program into memory at once – speeds up responsiveness to commands.

![[Pasted image 20240608171323.png | 300]] ![[Pasted image 20240608164856.png | 270]]

### Locality of Reference
In most programs if we look at memory access over a short period we see that only a small amount of the  address space used. This is known as the principle of locality of reference. Programs do not reference memory with a random distribution.

### Virtual Memory Paging
Virtual memory is commonly provided with paged memory. There are extra bits stored in each page table entry (and some of them in corresponding TLB entries).

![[Pasted image 20240608171748.png | center | 300]]
V - valid bit - is the page currently in real memory?
A - access bits - how can this page be accessed, read/write/execute?
M - mode bits - which mode does the processor have to be in when it uses this page
D - dirty bit - has this page been modified since this bit was last cleared?

Other bits could be there too, like referenced bit address. Either the frame number or the address on the disk device where this page is currently stored (this could be stored in a separate table) 

When a page is accessed, the page table entry indicates whether the page is currently in real memory or whether it is in a paging file (or swap space) on disk. The MMU takes care of translation between logical and physical addresses when pages are in real memory. If a page is not in real memory, it is up to the OS MMS to: Allocate real memory for the page, move pages from disk into memory, and indicate when the page is now ready
### Demand Paging
Chooses when a page gets loaded into real memory. When a process starts, all of its memory can be allocated (and loaded). If there is not enough real memory available, it has to be taken away from other processes. If there is still not enough, some has to go into swap space, Loading a large program can have a severe penalty on other processes in the system (and the overall amount of work done).

Demand paging only brings a page into real memory when the page is used by the process.
- When a process runs, its allocated memory but it all points to the swap space 
- Most demand paging systems load in the first few pages so the program can start without lots of page faults.

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
- Instructions must be restart able

![[Pasted image 20240609124009.png| center | 300]]

If we don’t have enough physical memory:
- Page replacement: If frame `f` (page `q`) is modified, write it back first.
- Change “`v`” to “`i`” for page entry (page `q`).
- Transfer the demanded data from backing store to physical memory.
- Modify the page entry (page `p`).

Now page `p` can be referenced to frame `f`

We only want this to occur one in every ~600,000 memory accesses. This can either be achieved through having lots of frames or repeated accesses. 

Frames can be allocated equally or proportionally depending on size or priority. 
- Can set minimum and maximum numbers per process.
- We really need the currently required pages in real memory.

#### Working sets
Uses locality of reference to keep a collection of pages needed in real memory to keep the process running. If we observe a process running over a short period of time (a window) we can record the page accesses the process makes. This is a picture of the page’s working set. If a window is too small not enough pages are included in the working set, If it is too big too many pages are included
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
There are two main ways of selecting frames for replacement, Global or local. Normally global replacement is chosen, but we might have a maximum number of frames for the process.

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
#### FIFO
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

