### Crash Course
#### Hello world
```c
#include <stdio.h> // Include directive, provides printf funciton

int main() {
	printf("Hello, World!\n")
	return 0;
}
```

#### Variable Declaration
```c
int interger_1; // Declear a new integer, modern compilers may set to 0
integer_1 = 5; // integer_1 is set to value 5
int integer_2 = 3; // int2 is declared and initialized in one line
double arr[10]; // declares an array of size 10
arr[0] = 3.1; // sets first element of arr to 3
```

#### Arithmetic Operations
```C
int x = 3;
int y = 2 * 2;
int z = 12 / 3; // z is initialized to be 4
z = x + y; // z is now 7
z = x - y * y; // z is now -13
double d = x / y; // d is 0.75
```
Care for casting

#### Control statements
```c
if() {} else {}
for(){}
while(){}
```

#### Functions
```
int foo(int x) {
	return x + 1;
}
```

### Advanced concepts
#### Pointers
A Pointer is an address in memory.
C pointers can have any integers assigned to them
- `int *p = 0xfff4;` `*p = 50;`

This means that actual addresses can be stored in a pointer and then used to access content over there.

Using pointers incorrectly can create big errors
##### Segment Fault
If a pointer tries to access an inaccessible address in a user-space program.
```c
int main() {
	int *pointer = 0xdeadbeef;
	printf("content in that pointer %d\n", *pointer)
}
```
The OS can catch this because user-space programs run in [[01 Intro to OS#Virtual memory |virtual memory]], so it has to be mapped into physical memory if there is no way to map it then there will be an error. 

![[Pasted image 20240301111817.png | center | 300]]


##### Kernel Panic
If the pointer tries to access an invalid pointer in kernel space. (what this mean>!??!?!?)
![[Pasted image 20240301112237.png | center | 400]]

##### Dangling Pointers
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

Rust is an emerging programming language that is providing solutions to memory safety.  

#### Memory Management 
Local variables for a function are allocated on the stack when the function is entered, Local variables are gone when the function finishes execution.

Global variables are allocated in data area when the program is started. They are only released when the program exits.

Static variables are allocated when the program starts, they are also only released when the program exits. 

```c
int global_var = 0;

int main() {
	int local_var = 1;
	static int static_var = 2;
	
	return 0
}
```

##### Dynamic Memory
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

##### Memory Layout
![[Pasted image 20240301112859.png | center | 500]]

#### Accessing registers
You can use the keyword `register` to hint that a variable should be kept in a processor register to reduce data access latency.
- `register int number = 1234;`

This does not guarantee that a value is stored in a register. Compilers may opt not to do so. Compilers do better than programmers in terms of register allocation (most of the time)

#### Volatile Variables
You can use the keyword volatile to specify that a variable may change unexpectedly.
- `volatile int a = 10;`

This requests compilers not to do optimisations on that variable.
![[Pasted image 20240301113131.png | center | 500]]

The other use case of volatile allows you to share a region of memory with other hardware. This will tell the compiler that this memory might be updated externally. 
#### Inline Assembly
![[Pasted image 20240301113416.png | center | 400]]

### C Compilers
There are two main options available
- GCC - This is almost 40 years old, very stable, the "gold standard"
- Clang (LLVM) - Modern architecture, faster compilation times, more advanced optimisations, but less stable. Not often used for OS dev but common in user-space dev

Compile using gcc
`gcc path/to/file.c`

generates by default `a.out`

to run: `./a.out`

`gcc file.c -g -02` -g attaches debugging, -02 do optimisation on source code for better binary

#### GDB
GDB is a debugger for C, it works with `-g` GCC compiled files. Gives good debugging info
#### LLVM Compiler infrastructure
The LLVM Infrastructure Provides the availability for you to make your own language. Just write a few hundred lines of code. You just write the frontend, and LLVM handles the rest of the conversions

![[Pasted image 20240301113701.png | center | 500]]
