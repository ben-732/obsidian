### Process State 
As a process executes it changes between 5 different states
- New - The process is being created
- Rady - The process is waiting to be assigned toa  processor
- Running - Instructions are being executed
- Waiting - The process is waiting for some event to occur
- Terminated - The process has finished execution

![[Pasted image 20240409140130.png | center | 500]]

### Runnable
A runnable process means it is either running or ready to run, We usually have many runnable processes but a CPU Can only run one process at a time. 

The kernel decides which runnable to execute on the core at any given time. 

By jogging between different processes very fast, we can achieve multi-tasking. 

### Multitasking Models
#### Corporative Multitasking
The style of multitasking discussed in [[07 Threads#User level (Green) Threads|green threads]]. 

This requires a process to be considerate and voluntarily yield its right to run from time to time. A selfish process can make the computer unresponsive. 

This is normally only implemented in old operating systems because uncooperative processes lead to too many issues. 


#### Pre-emptive Multitasking
Each process is assigned a specific amount of CPU time. Upon reaching the time limit, the CPU halts the current process and works on other processes. 

Every runnable will be executed without having to worry about "selfish" processes

### Context Switch
The change from one running process to another on the same processor is usually referred to as a context switch. 

A context is:
- Registers
- Memory
- Files
- Etc
Context is stored or restored when switching process, the context of a process is stored in the PCB

![[Pasted image 20240409141426.png | center | 400]]

### Waiting
It is very rare for a process to have all the resources they need when they start. 
- Data from files (Slow hard disk IO)
- Data from network (Slow internet speed)

Waiting processes are suspended and put into a waiting queue. 

When the resources become available, the process is removed from the waiting queue and becomes runnable again. 

![[Pasted image 20240409141727.png | center | 400]]

#### Shell Waiting 
In a terminal shell, you can use `Ctrl + Z` to temporarily stop a processes (Set it to the waiting state) 

This allows other processes to run to completion more rapidly. 

You can use `jobs` to show all stopped processes and `fg` to resume a process

### Termination
Process executes last statement and then asks the operating system to delete it using the `exit()` system call. Or simply return from the main function in C, an `exit()` system call will be made implicitly. 

Parent process calls `wait()` to wait for the child to terminate

### Scheduling
A schedular determines the order in which processes are executed from the ready queue. 

It tries to acheive maximum CPU utilization. In rea life, most programs feature a *CPU - I/O Burst Cycle*.

This is wear CPU Execution is followed by I/O Waiting

However, the CPU Burst is generally very small and the IO is quite slow. 

For example a 1ms CPI burst is followed by 100ms of I/O Wait. The CPU Utilisation is less than 1%

![[Pasted image 20240409142557.png | center | 300]]


The CPU scheduler selects from among the processes in the ready queue (i.e., ready to do CPU burst) and allocates a CPU core. 

CPU scheduling happens periodically. 

CPU scheduling decisions may take place when a process 
1. Switches from running to waiting state 
2. Switches from running to ready state 
3. Switches from waiting to ready 
4. Terminates

![[Pasted image 20240409140130.png | center | 500]]

#### CPU Dispatcher
The dispatcher module gives control of the CPU to the process selected by the CPU scheduler.

This involves: 
1. Switching context 
2. Switching to user mode 
3. Jumping to the proper location in the user program to resume that program 

Dispatcher Latency is time it takes for the dispatcher to stop one process and start another running

#### Scheduling policies
To evaluate a scheduling policy you should look at:
- **CPU utilization** - keep the CPU as busy as possible 
- **Waiting time** - amount of time it takes from when a request was submitted until the first response is produced. 
- **Throughput** - # of processes that complete their execution per time unit 
- **Turnaround time** - amount of time to execute a particular process

##### First Come First Served (FCFS)
1. non pre-emptive
2. Easy to determine which process should run next. 

![[Pasted image 20240409144745.png | center | 500]]

The waiting time for a process is the duration from the moment it starts queuing until it begins processing. 

Average waiting time: $(0 + 24 + 27) / 3 = 17$

![[Pasted image 20240409144827.png | center | 500]]

**Convoy Effect**: a lengthy process arrives first and takes a substantial amount of time to complete, causing all the shorter processes behind it to experience prolonged waiting times.
##### Shortest Job First (SJF)
![[Pasted image 20240409144852.png | center | 500]]

Average waiting time $= (0 + 3 + 9 + 16) / 4 = 7$

SJF in theory can gives minimum average waiting time. Unfortunately, we usually don’t know which process has the shortest CPU burst.

##### Pre-emptive SJF
Whenever a new process arrives in the ready queue, with a shorter burst time than the remaining burst time of the running process then the process is pre-empted

![[Pasted image 20240409150007.png | Center | 500]]

How much time the process stay in the ready queue without doing anything. 
$P1 = 11 - 2$ 
$P2 = 5 - 4$ 
$P3 = 4 - 4$ 
$P4 = 7 - 5$ 

Average waiting time $= (9 + 1 + 0 +2)/4 = 3$
###### Starvation
What would happen if there is a continuous influx of short processes. Starvation 

Solution: the priority of a process increases the longer it waits in the queue

##### Round-Robin
Treat everyone equally, Give everyone a time slice to run. If not finished with the time slice, move to next process.

![[Pasted image 20240409150235.png | Center | 400]]

Average waiting time $= (6 + 4 + 7)/3 = 5.6$

##### Priority Scheduling
Each process is pre-assigned with a priority.

![[Pasted image 20240409150349.png | center | 500]]

Explicit Priorities: Set a priority before a process runs. The priority never changes. 
- Starvation: jobs with higher priority keep coming, so lower priority tasks never get executed. 

Varying Priorities: A job priority can varying during its lifetime. 
- Starvation prevention: as time progresses, the priority of the process increases.

##### Nice
In Linux/Mac OSX, you can use the `nice` command to start a process with a specific priority. 

Nice value range from `-20` (highest) to `19` (lowest).

#### Multilevel Queue Scheduling
Different queues may use different scheduling algorithms.

Different queues can have different priorities (e.g., always dequeue from the system processes first) 

A process may switch its queue.

![[Pasted image 20240409150544.png | center | 500]]

## Multi-core Scheduling
There are many ways to increase the speed of computation, a good one is increasing the number of logical cores

There are two main architectures for creating multi-core CPUs

### Multi Core Models
#### SMP
-  Most off-the-shelf CPUs are Symmetric MultiProcessing.
- It has multiple processors, and they are identical.
- They share the bus, memory, and I/O devices.
- They have same access latency.

This does not scale well because only one core can access memory at a time

![[Pasted image 20240507091009.png | center | 300]]
#### NUMA CPUs
Non-uniform memory access CPUs.

- Each CPU has its own private memory, thus no need for arbitration and waiting.
- Some memory regions are faster to access.

#### Comparison
**SMP Architecture:**
Simplicity: SMP systems are relatively simple to design and implement.
Scalability: The shared bus architecture can limit scalability as the number of processors grows.

**NUMA Architecture:**
Simplicity: NUMA architectures can be more complex to design and maintain.
Scalability: NUMA scales better as the number of processors increases.

#### Scheduling
##### SMP
For a SMP CPU, two possible ways to schedule tasks:
- All runnable tasks may be in a single queue
- Or each processor may have its own private queue

![[Pasted image 20240507091229.png | center | 400]]
##### NUMA
It is a generally challenging problem, the main goal is to improve memory locality and reduce access latency. 

### Distributed Computing
Multiple computers work together to solve a common problem.

Each computer itself can have their own process architectures. Computers communicate with each other over network protocols. These are often not fast or even reliable. 

 Scheduling becomes even harder with increased state inconsistency and deadlock

The two general problem applies here, and has been proven to be unresolvable. 

However, in real life we can design a robust communication protocol to reduce the chances of state inconsistency. 

### Distributed Deadlock Detection
Each node keeps track of the [[09 Deadlock#Resource Graphs|resource allocation graph]] to do with its local resources.

A central node is used to gather and union the resource graphs from different nodes. The resource graphs can be outdated sometimes. It is an approximation algorithm. 

![[Pasted image 20240507092030.png | center | 300]]
### Distributed Deadlock Avoidance
1. Resource total ordering.
2. Banker Algorithm.
	- All requests have to be checked by a Banker process. It becomes a single point of failure
3. Process total ordering.

#### Total Process ordering
Main idea: Prevent this circle by killing/restart younger processes

If an older process asks for a resource from a younger process, it waits for the process to finish then continues. If its the other way around and the older process asks for a resource from a younger process, the younger process dies and restarts after the older process. 

![[Pasted image 20240507092240.png | center | 300]]
**Timestamp Prevention algorithm**
Wait-Die: Process A requests a resource held by process B. If process A is older (smaller timestamp) than process B, it waits for the resource. Otherwise process A dies/restarts, process B (the older) continues.

Would-Wait: Process A requests a resource held by process B If process A is older (smaller timestamp) than process B, it takes the recourse and restarts B. Otherwise process A just waits.

Processes keep their timestamp even they restart

![[Pasted image 20240507092431.png | Center |200]]

### Real time scheduling
Used in realtime applications

**Hard** real times systems: Task must be serviced by its deadline. 
**Soft** real time systems: Critical real time tasks have the highest priority, but no garantee on deadline. 

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
