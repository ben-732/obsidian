Each process in the modern operating system operates independently by default. This means that processes do nterfere with each other's execution. Each process has its own address space. Processes cannot directly access each other's memory. 

However there are benefits that can be gained from processes being able to communicate with each other. Common benefits include:
- Computation speedup
- Information sharing and protection
- Modularity

#### Apache Web server
The Apache webserver is one example of a multi-processing model. 

The parent process monitors for http requests. When it detects one, it forks a child process to handle it. 

The parent and child processes can exchange information. 


### Methods
Two classic methods of IPC
- Message Passing
- Shared memory

![[Pasted image 20240312191209.png | center | 300]]

#### Pipe (Message Passing)
In Unix based systems, you can create  a pipe using `pipe()`. Pipes are treated as normal files. 

`int my_pipe[2]; pipe(my_pipe); fork();`

Because the child and parent process are identical, they share opened files. 
![[Pasted image 20240312191420.png | center  ]]

- Parent reads data from child `read(fd[0])`
- Parent sends data to child `write(fd[1])`

**Empty / Full pipes**
When trying to read something from an empty pipe your program will freeze until data becomes available. Your program will also stop and wait if it is full (>64KB)

**Broken Pipes** (When one end has crashed)
A process waiting to read from a pipe with no writer gets an EOF
A process writing to a pipe with no reader gets signalled and killed. 

**Named Pipes**
Named pipes are more powerful, no parent child relationship is required. 
Several processes can use the naked pipe for communication. Windows also implements a named pipe. 

`mkfifo()` with a file path to create a named pipe. The pope looks like a normal file and can be read/written by different processes. 

These have the trade off of being one directional, 

Writing:
![[Pasted image 20240312192018.png | center | 300]]

Reading:
![[Pasted image 20240312192134.png | center]]

