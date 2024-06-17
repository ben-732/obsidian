A threads is effectively a lightweight [[04 Process Creation|process]].

A process is an instance of a program that runs in its own memory space. A thread however is a basic unit of executions within a process with their own registers and stacks while sharing the process resources (Code, data, files and memory). 

A process can have multiple threads and allow different aspect of a process to run concurrently.   

![[Pasted image 20240320155024.png | center | 350]]

#### Inter thread communication
Because the threads are sharing the same memory we don't need ICP to communicate, instead we can just do in memory communication

### Thread Applications
Ideally, we should use an amount of threads equal to the number of CPU cores. This ensures efficient use of available resources while minimising context switching overhead. 

An Example of an application of threading is the MYSQL[[01 Intro to Databases#Database systems | DBMS]], here each client connection is handled by a separate thread. This allows those clients to execute queries and transactions concurrently without impacting each other. 

Another application of threading is multithread sorting. This is using a [[4. Sorting#Merge Sort |divide and conquer]] sorting technique where the array is split into multiple smaller arrays which are sorted in different threads before being merged back into one array.  

![[Pasted image 20240320155647.png | center | 300]]

#### GUI Applications
Every GUI application has a main thread to manage the user interface (UI) and respond to user interactions. Doing heavy computation int he main thread will freeze the application. Similar to the observed behaviour in the 206 concurrency examples.

![[Pasted image 20240320155945.png | center | 400]]
>An example of heavy processing freezing the UI

This is known as "application not responding (ANR)" causes some commonly seen error messages in prompts asking if you want to wait or end the process. 

A well written GUI application will have at least two threads, one that is responsible for UI elements and interaction. As well as other "Job" threads to do all heavy computation. This adds responsiveness to GUI applications by allowing the UI to be rendered/interacted with during heavy computation

![[Pasted image 20240320160258.png | center | 400]]

### Thread Implementations
There are two types of implementations for threads

**System level threads:**
- These are constructed and controlled by system calls. 
- The kernel knows the state of each thread. 
- You do not have much control here. 

**User-level Threads:**
- Threads are managed by the runtime or library. Not the kernel
- This comes with a lower overhead. 
- The kernel is not involved much at all. 

#### System level threads
There is a "very famous" library called POSIX  Threads or `pthreads` that is a standardised API used for creating and managing threads in Unix-like operating systems. `pthreads` provides a portable and efficient way to implement concurrent programming.

##### Creating Threads
To create a new system level thread you use `pthread_create`

```c
int pthread_create(pthread_t *thread, const pthread_attr_t *attr, void *(*start_routine)(void *), void *arg);
```

- `pthread_t`: Thread ID variable. 
- `pthread_attr_t`: Thread attributes (optional, use `NULL` for default). 
- `start_routine`: Pointer to the function executed by the thread. 
- `arg`: Argument passed to the `start_routine`.

##### Joining Threads
The other main function that POSIX Threads provided is `pthread_join` which is used for waiting and synchronizing with the termination of a thread.

```c
int pthread_join(pthread_t thread, void **retval);
```
- `thread`: Thread ID to wait for. 
- `retval`: Pointer to a variable where the exit status of the joined thread is stored.

![[Pasted image 20240320164458.png | center | 500]]

#### User level (Green) Threads
Managed by runtime or library, not the kernel. Lower overhead as no user-kernel switch. 

The kernel provides one system level thread to the process. The process "splits" this thread for multiple tasks.  This concept exists in may high-level languages like Python, Java and JS.

![[Pasted image 20240320165701.png | center | 400]]

The reasoning for using green threads over system threads is to do with the different types of CPU task load patterns.  CPU Bound and IO Bound

![[Pasted image 20240320165958.png | center | 400]]

in IO bounded tasks, most of the time is spent wating for IO tasks to happen. With green threads, we can increase the utilisation of CPU time by filling in gaps during long IO operations.

##### Why Green Threads?
The C10k problem: To handle 10K network connections at the same time. 
- Too costly to create 10K system-level threads. 
- Bookkeeping threads or processes in kernel has high overhead. 
- For example, 1MB for each thread, then we need 10 GBs.

Instead we can use green threads:
- Idea: A significant portion of the time is spent waiting for data to be sent or received over the network (i.e., doing nothing) 
- Green threads have a very low bookkeeping overhead (no user-to-kernel mode switch)

![[Pasted image 20240320170346.png | center | 400]]

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

# Wait for green threads to finish
gevent.joinall([greenlet1, greenlet2])
```

A green thread need to voluntarily stop its current job from time to time and let other threads execute (cooperative tasking). Yield its CPU by calling IO, networking and sleep functions. 

A bad example:
```python
def task1():
	# Do a very complex math computation, eg calculating pi

def task2():
	for i in range(5):
		print("Task 2 working")
		gevent.sleep(1)

# Create green threads
greenlet1 = gevent.spawn(task1)
greenlet2 = gevent.spawn(task2)

# Wait for green threads to finish
gevent.joinall([greenlet1, greenlet2])

```

The very complex task 1 will hog the CPU time and will not give task 2 a chance to run...
![[Pasted image 20240320171750.png | center | 400]]
becomes like
![[Pasted image 20240320171805.png | center | 400]]
### Asynchronous IO
A more modern paradigm to handle IO bound tasks. Conceptually similar to green threads. 

The idea is that you yield to other tasks when the IO operation is underway. 

Coroutine
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

>[!info]- Coroutine
>Coroutines (Co Routines) are computer program components that allow execution to be suspended and resumed, generalizing subroutines for cooperative multitasking

