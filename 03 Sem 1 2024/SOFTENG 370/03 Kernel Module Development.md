In this course we focus on linux development because its open source and well documented. It is also widely used across a range of applications like super computers, steam deck and more... 

Principals here can be transferred to other kernels. 

### Shell
The program that takes input from the user and gives them to the operating system to preform. 
For some tasks it is more convenient than using a GUI

![[Pasted image 20240305091601.png | center | 500]]

### Writing a linux kernel module
#### 1 - Get a virtual machine

Use a linux virtual machine to protect your system as mistakes could crash / destroy your OS or Computer. QEMU, VirtualBox, Hyper-V are all recommended

##### How it works

1. Fetch: Get a GBA instruction from the game. (For example: ADD R3, R3, #5) 
2. Analyse: It adds 5 to the register R3. 
3. Translate & Execute: register_3 = register_3 + 5 
4. Go to step 1.

###### Interpretation
Interpretation: fetch a CPU instruction, analyse it, perform the operation
Can execute code for different CPU architectures.
Con: high execution overhead for each instruction

###### Binary Translation
Binary translation amortises the fetch and analysis costs by
1. fetch and translate a block of source instructions to target instructions. 
2. Saving the translated code for repeated use.

###### Binary Translation vs Interpretation
Generally binary translation has much higher performance results, it is roughly 10x faster in most situations. 
![[Pasted image 20240305092323.png | center | 300]]
##### Hardware Virtualisation
- Translation and analysis are unnecessary if code is compiled for the same CPU architecture. 
- Consider running a Ubuntu x86-64 on a Windows x86-64 machine. 
- Ubuntu is the guest OS, Windows is the host OS

Modern CPUs use dedicated circuits to isolate programs between host and guest OS.Secure Virtual Machine (SVM) in AMD, Virtual Technology (VT) in Intel. This results in very low overhead. 

#### 2 - Install a Linux OS
Ubuntu, Debian, etc
Most popular distrobutions are good for general use. 

#### 3 - Install Essential Tools
Run the following command in your terminal:
```shell
sudo apt-get install build-essential linux-headers-$(uname -r)
```
This installs the C compiler and necessary C headers

#### 4 - Create the source Code
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
#### 5 - Create the makefile
```shell
obj-m += dummu_module.o

all: 
	make -c /lib/modules/$(shell uname -r)/build M=$(pwd) modules
```
#### 6 - Debug & Test
1. `make`: Compile your source code into a module. (`hello_world.ko`) 
2. `insmod`: Load a module. (`insmod hello_world.ko`) 
3. `dmesg`: Show the kernel log. 
4. `rmmod`: Unload a module

Makefile is a script to automate the process of building executable programs. 
- `obj-m += dummy_module.o` Specifies the source file for the module

### Everything is a file
In linux (at least) everything is a file, this includes all hardware/devices etc,
- Your keyboard is /dev/input/keyboard1 
- Your mouse is /dev/input/mice 
- Your Nvidia GPU is /dev/nvidia 
- Random number generator /dev/urandom 
- Zero generator /dev/zero

#### LED Example
For instance, a kernel driver responsible for managing an LED light can show itself as a file in the hard disk. 
- By writing zeros or ones, you can control the activation and deactivation of the LED light. 
- By reading from the file, you can determine whether the LED is on or off.

### Odd/Even Example
In the initialization function, the module registers the device as a file using `register_chrdev` functions. 

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
### Assignment 1
Develop a Linux kernel module which exposes itself in /dev/prime_number. 

The module is capable of receiving an unsigned 32-bit integer input from a user program. 

The module should determine whether the provided integer is a prime number and return either 1 (if prime) or 0 (if not prime). A python test case is as followed.

![[Pasted image 20240305094837.png | center | 500]]

Submit a single C file. 
- 2 point for successful compilation, `insmod` and `rmmod` 
- 6 points for passing 12 test cases 
- 2 points for finishing all test cases within 200ms in AMD 5800x CPU.