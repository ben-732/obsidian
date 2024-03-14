A process is used to represent ongoing work in the system.

More formally, an instance of a program in execution.
- A program: A program is passive entity stored on disk (e.g., exe file). It includes code and data.
- An instance: there may be multiple processes coming from the same program.
- In execution: It needs the processor to run.

A process can own resources (CPU times, Memory, and Files)

On unix systems, you can use the command `ps aux` to get  list of processes. 
Alternatively, the `top` command shows all processes in real time. 


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
![[Pasted image 20240311092800.png | center]]

### Creating a process
For a user its really quite easy, simply just click on a executable (or a shortcut to one). Alternatively type the program name into shell. 

Internally the operating system has to do more work
- Allocate a new PCB
- Memory Allocation
- Setup the execution context (setting up entry point and registers)
- Initialisation of Process State
- Execution of the Process

#### Linux process creation
A parent process create children, which in turn, create other children.
- This forms a tree of processes.

this is done with the `fork()`  system call. 

`fork()` duplicates the parent process (the process that calls `fork`)

![[Pasted image 20240311093257.png | center | 300]]

Fork returns 0 in the child process and the child's pid in the parent. This allows us to tell whether the code is running in parent or child process.
- The child and parent process share open files as well.

#### Exec()
In linux, a child process can use the system call `exec()` to replace its memory space with a new program. 

We can combine `fork()` and `exec()` to create a new process. 

Coping a process using fork and immediately release it using exec seems like a bit of a waste.  Linux uses COW (copy on write) to address this issue.

COW effectively just delays the copy until the value is modified, as we are often going to immediately call exec(), it will never actually be copied. 

 https://www.tutorialspoint.com/copy-on-write-in-operating-system

#### Exit()
`void exit(int status);`

Terminate the process immediately, Implicitly called, when return from the **main** function.

#### Wait()
A call to `wait()` blocks the calling process until its child processes exits or a signal is received.
- After child process terminates, parent continues its execution



#### Summary
![[Pasted image 20240311094312.png | center ]]

#### Kill()
A parent process can terminate a child process using the kill.
- int kill(pid_t pid, int sig);

### More
init process is root node

