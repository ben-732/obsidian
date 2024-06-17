A Deadlock occurs when a processes enters a waiting state because a requested system resource is held by another waiting process. Which in turn is waiting for another resource held by another waiting processes. 

A circle of processes each holding a resource wanted by another process in the circle.

![[Pasted image 20240408233528.png | center | 400]]

For a deadlock to occur, there are four conditions that need to happen simultaneously:
1. **Hold and wait** - A process can hold a resource while requesting another
2. **Mutual exclusion** - Only one thread at a time can use a resource. Resources cannot be shared. 
3. **No Pre-emption** - Only the owner can release the resource
4. **Circular wait** - There is a circular list of processes each wanting a resource owned by another in the list. 

### Resource Graphs
This is a data structure that represents resources and processes as vertices in a graph. 
- Resources are represented by rectangles 
- Processes are represented by circles
- Allocations and requests are directional edges

A loop in the graph indicates a potential deadlock

![[Pasted image 20240408234134.png | center | 300]]

This is a necessary but not sufficient requirement. 

### Deadlock prevention
To prevent a deadlock, you must invalidate one of the four necessary conditions. 

#### Mutual Exclusion:
- Make the resources sharable
- Don't put locks on sharable resources (e.g. read only files)

#### Hold and Wait
Only do One resource at a time?
Make sure that when a thread requests a resources, it does not hold any other resource. 

##### 5 Philosophers problem
5 Philosophers at a round table with a fork in between each adjacent pair. A philosopher needs to pick both the fork to their left and to their right in order to be able to eat. 

Solution: use `simultaneous_wait`
```
status = "waiting"
simultaneous_wait(left, right)
status = eating
signal(left)
singal(right)
```

Simultaneous wait looks something like 
```
while True:
	right.lock()
	
	if left.try_lock():
		break
	
	right.unlock()
```

`pthread` has a `trylock` method built in. 

`int pthread_mutex_trylock(pthread_mutex_t *mutex);`

This function is the equivalent to `pthread_mutex_lock()` except that if the mutex object referenced by `mutex` is currently locked, (by any thread including the current thread) the call shall return immediately. 

#### No Pre-Emption
Allow a resource to be forcibly removed from a process. This is very difficult to implement as it causes processes to break. 

One example would be restarting your computer if your OS enters deadlock. This kills all the processes to removes all waits ?

#### Circular wait
Impose a total ordering of all resource types.

Each thread requests resources in an increasing order of enumeration. If you have already obtained resource 3 then you cannot request resource 1. 

##### Partial vs Total ordering
A **Total ordering** means that ever element of the set is comparable with every other element in the set. One example of this are natural number, $1 \leq 2, 2\leq 100$

In a **partial ordering** however not every pair of elements is comparable. 
- With vectors, we may define $[1, 2] < [3, 4]$ but what about $[1, 4]$ and $[2, 3]$

### Recover from deadlock
#### Killing processes
The first way is just to **kill the processes** involved. Force it to give up the resources. 

You can kill all related processes but this might cause large disruptions. Alternatively you can just kill the processes of least importance but this might result in the deadlock occurring again immediately. 

#### Checkpoints and Rollbacks
Another method is using **checkpoints & rollbacks**. Roll back to a safe state to release the resource. 
Here the system needs to maintain checkpoints

*Starvation* is where the processes repeatedly rolls back to the same point and doesn't achieve anything. 

### Deadlock avoidance algorithm
Before granting resources, we check if deadlock could occur if we allocate this resource to this process. 

Challenge: Although a system knows who has what it doesn't usually know who wants what. 

A conservative solution would be to assume a process may need all relevant resources, the drawback of this is that a processes might et stopped from getting a resource even though it is available. 
#### Banker Algorithm
The basic information required is:
```
Total system resources are:
A B C D
6 5 7 6

Avaliable system resources are:
A B C D
3 1 1 2

Processes (Currently allocated resources):
   A B C D
P1 1 2 2 1
P2 1 0 3 3
P3 1 2 1 0

Processes (Maximum resources):
   A B C D
P1 3 3 2 2
P2 1 2 3 4
P3 1 3 5 0
```

```
Need = max - cirrently allocated
Processes (Possibly needed resources)
   A B C D
P1 2 1 0 1
P2 0 2 0 1
P3 0 1 4 0
```

`P1` needs `2 1 0 1` (`2A`, `1B` and `1D`)more resources. This is available so we grant it, available resources becomes
```
3 1 1 2  -  2 1 0 1 = 1 0 1 1
```

After granting the resource, we assume `P1` will finish and thus release the resource

```
1 0 1 1  +  3 3 2 2 = 4 3 3 3
```

We follow a similar processes for `P2` and after it finishes we have `5 3 6 6`
Same for `P3` then all resources are available with no deadlock.

If `P2` was asking for heaps of resources, we would not be able to grant it and there would be a deadlock in the middle.?

### Livelock?
Where processes keep running but no actual progress is made. 

An example of this is when two people meet face to face in a corridor. Both moving side to side in sync so neither can get past. 




