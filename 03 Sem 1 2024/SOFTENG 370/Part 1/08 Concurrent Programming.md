This is a program that arises from having multiple threads/processes running at the same time at their own pace. 

When they try and access the same data (structures/objects/devices) at the same time problems can occur from some resources (like a printer) only being able to be safely used by one thread at a time.  

**Bank Account Example:**
![[Bank account concurrency example.png| center | 500]]

Here two instances of adding $1 to an account happen at the same time, this causes the read operations to happen incorrectly and only $1 gets added instead of $2. 

Another example of this is the counter question from last lecture, 
![[Pasted image 20240320172905.png | center | 400]]
Here the final value of counter is indeterministic as it is not guaranteed that two increment operations won't try increase the value of counter simultaneously. 

In this case, `counter++` was translated into three separate assembly instructions:
 1. Read original value from the memory to a register 
 2. Add 1 to the register 
 3. Write the register value back to the memory

### Race Conditions
A race condition is any situation where the order of execution on processes/threads can cause different results. 

Non-deterministic phenomenon is due to CPU scheduling. 

### Critical Sections
A sequence of code in which we only want one thread to be active at a time. If there is already a thread in the section, then we wait for the thread to leave first before executing. 

Top up $1 to account
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

![[Pasted image 20240320173456.png | center | 200]]
**Wrong** critical section implementation
```
Enter_Critical_Section: 
	1. while locked: do nothing. 
	2. locked = true 

Leave_Critical_Section: 
	1. locked = false
```

This is bad as you could get a situation where:
1. Initially `locked = false` 
2. `P1` go ahead first, because `locked` is `false`, `P1` can skip the loop. Before moving to instruction 2, it yields to `P2` due to CPU scheduling. 
	1. `while locked: do nothing` - `P1` skip the loop, ready to move to next instruction, but it yields to `P2`. 
	2. `locked = true` 
3. `P2` go ahead now, because `locked` is still `false`, `P2` can also skip the loop and execute the instruction 2. 
	1. `while locked: do nothing`. - Ready to move to instruction 2
	2. `locked = true` - `P2` enter the critical section 
4. When `P1` resumes, it will enter the critical section straight away . 
	1. `while locked: do nothing`. 
	2. `locked = true` - P2 enter the critical section, P1, enter the critical section\

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
Enter_Critical_Section: 
	While testAndSet(locked): do nothing 

Leave_Critical_Section: 
	locked = false
```

##### Compare and Swap (CAS)
Another instruction is `CAS`. There are 3 parameters for a `CAS` operation: `CAS(V, A, B)` 
1. A memory location `V` where value has to be replaced 
2. Old value `A` 
3. New value `B` which should be written over V 

`V` should have the value `A`; if it does, put `B` there. i.e., the swap takes place only under this condition.

```
Enter_Critical_Section: 
	While !CAS(lockedVariable, false, true): do nothing 

Leave_Critical_Section: 
	lockedVariable = false
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

This is something from the lecture slides?:
```c
bool __atomic_compare_exchange(size_t size, void *ptr, void *expected, void *desired, int success_order, int failure_order)
```
#### Spin Lock
Have the CPU do busy waiting (looping) to get the lock. This is called spin-lock. 

This used often as it is easy to implement and generally the time required to wait is minimal so it is acceptable to do this. 
![[Pasted image 20240320175053.png | center | 400]]

```c
#include <linux/spinlock.h>

static DEFINE_SPINLOCK(my_spinlock); // Define a spinlock

spin_lock_irqsave(&my_spinlock, flags); // Aquire the spinlock

spin_unlock_irqrestore(&my_spinlock, flags); // Unlock
```

#### Atomic Variables
For if you just want to update a variable with a basic type atomically.

Use type specifier `_Atomic`
- `Atomic_load`, `Atomic_store`, `Atomic_fetch_add`

```c
int main() {
	// Declare an atomic integer varaible
	_Atomic int atomicVar = 0;

	// Perform atomic increment
	atomic_fetch_add(&_AtomicVar, 1);
	
	// Perform atomic load
	int value = atomic_load(&_AtomicVar);
	
	printf("Atomic varaible value: %d\n", value);
	
	return 0;
}
```

#### Mutex
Mutex lock: Boolean variable indicating if lock is available or not. A wrapper for low-level hardware instructions

Declare a mutex by `pthread_mutex_t mutex;` Enter critical section by calling `pthread_mutex_lock(&mutex)`

When you try to lock a locked mutex from a different thread, the second thread will wait for the first thread to unlock before continuing. 

![[Pasted image 20240320181720.png | center | 500]]

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

##### Linux kernel mutex
```c
#include <linux/mutex.h>
static DEFINE_MUTEX(my_mutex); 
mutex_lock(&my_mutex); 
mutex_unlock(&my_mutex);
```

#### Semaphore
An advanced version of Mutex Lock. The semaphore variable is an integer now

For example, you have three printers, so you allow 3 threads to use them at the same time. 

To get a resource the thread calls `sem_wait()` on the semaphore. It waits for the semaphore to become positive and then decreases the variable by `1` (`s -= 1`). 

To return the resource, the thread calls `seg_signal()`. It increases the semaphore by 1 (`s+=1`).

`sem_init(&semaphore, false, 1);` Creates a semaphore with 1 thread that can access. 

```c
void* threadFunction(void* arg) {
	int thread_id = *(int*)arg;
	
	// Wait (decrement) the semaphore
	
	// Critical section
	printf("Thread %d entering critical section\n", thread_id);
	sharedVaraible++;
	printf("Thread %d exiting critical section\n", thread_id);
	
	// Signal (increment) the semaphore
	sem_post(&semaphore);
	
	pthread_exit(null);
}
```

#### Read-Writer Lock
The Reader/Writer Problem is where there are a number of thread, and to preserve the integrity of the shared data, we need to ensure:
- If no writer is active there can be multiple readers
- If a writer is active there must be no other active readers/writers. 

`pthread_rwlock_init` Initialises a `rwlock`
`pthread_rwlock` acquires a read lock. 

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

![[Bank account concurrency example.png| center | 500]]

Here we could use many of the previous examples to solve this issue, or the following lock free algorithm.  

```python
def add_to_balance(increase);
	previous_amount = balance
	while(!cas(&balance, previous_amount, previous_amount + increase)):
		previous_amount = balance
```

Idea: if the `cas` returns `false`, It means someone toped up the account when you trying to top up the account. Just get the new balance, and do the `CAS` again.

#### Lock-Free Data structures
Many high-level languages provide lock-free data structures such as [[7. Data structures#Java Collections|stacks, queues, and sets]]. 

Internally, they may use compare and swap hardware instructions

### Producer and Consumer problem
![[Pasted image 20240320184717.png | center | 300]]
When you have a producer and a consumer both working on one buffer, you can have problems if the producer an consumer are operating at different speeds. Producer can't add if the buffer is full and the consumer cannot consume if the buffer is empty. 

#### Potential Solution: Semaphores
Setup two semaphores:
- One for number of empty slots, 
- One for number of occupied slots. 

Consumer decreases number of occupied slots by 1 (`sem_wait`) and increases number of empty slots by 1 (`sem_signal`). 

Producer decreases number of empty slots by 1 and increases number of occupied slots by 1.

```python
empty = threading.Semaphore(BUFFER_SIZE);
full = threading.Semaphore(0);

## Producer Function
def chef():
	while True()
		empty.wait() # Wait for then decrement empty slots
		# Cook new meal
		time.sleep(1)
		full.signal()   # Increment number of slots filled

def hungry_person():
	while True()
		full.wait() # Wait for then new meal then eat (decrement semaphore)
		# Eat meal
		time.sleep(2)
		empty.signal()   # Increment number of empty slots

```

### Life Advice
Don't write concurrent code if you can avoid it. 


#### Some problems
Not unlocking properly in lock
![[Pasted image 20240320185309.png | center | 300]]

Overuse of locks, can make angry circle things
![[Pasted image 20240320185237.png | center | 300]]

#### Rules for writing concurrent code
1. Don't do it, if you can avoid it. 
2. If you must do it, don't share data across threads 
3. If you must share data across threads, don't share mutable data 
4. If you must share mutable data across threads, synchronize access to that data 
5. Use high-level languages designed for concurrent computation, for example, `Go`

#### Go Programming Language 
Go uses channels to connect threads for data sharing. 
- `ch <- data // Send data to channel ch`. 
- `data:= <-ch // Receive from ch, and assign value to data.` 

Internally, a channel generates data copies, so no concurrency issues