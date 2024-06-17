Each [[04 Process Creation|process]] in the modern operating system operates independently by default. This means that processes do interfere with each other's execution. Each process has its own address space. Processes cannot directly access each other's memory. 

However there are benefits that can be gained from processes being able to communicate with each other. Common benefits include:
- Computation speedup
- Information sharing and protection
- Modularity

### Apache Web server
The Apache webserver is one example of a multi-processing model. 

The parent process monitors for http requests. When it detects one, it forks a child process to handle it. 

The parent and child processes can exchange information. 


## ICP Methods
Two classic methods of IPC
- Message Passing
- Shared memory

![[Pasted image 20240312191209.png | center | 300]]

### Pipe (Message Passing)
In Unix based systems, you can create  a pipe using `pipe()`. Pipes are treated as normal files. 

`int my_pipe[2]; pipe(my_pipe); fork();`

Because the child and parent process are identical, they share opened files. 
![[Pasted image 20240312191420.png | center  ]]

- Parent reads data from child `read(fd[0])`
- Parent sends data to child `write(fd[1])`

#### Edge case pipes
**Empty / Full pipes**
When trying to read something from an empty pipe your program will freeze until data becomes available. Your program will also stop and wait if it is full (>64KB)

**Broken Pipes** (When one end has crashed)
A process waiting to read from a pipe with no writer gets an EOF
A process writing to a pipe with no reader gets signalled and killed. 


#### Named Pipes
Named pipes are more powerful, no parent child relationship is required. 
Several processes can use the naked pipe for communication. Windows also implements a named pipe. 

`mkfifo()` with a file path to create a named pipe. The pope looks like a normal file and can be read/written by different processes. 

These have the trade off of being one directional, 
#### Using Pipes
Writing:
![[Pasted image 20240312192018.png | center | 300]]

Reading:
![[Pasted image 20240312192134.png | center]]

### Sockets
A socket is defined as an endpoint for communication.

#### Internet Socket
A common form of socket is an internet socket.  It is identified by an IP address and a port.


The socket `10.0.0.1:1625` refers to port `1625` on host `10.0.0.1`

An IP(v4) address is a 32 bit sequence of 1s and 0s
Special IP address `127.0.0.1` (localhost) refers to the host itself. 

Ports below `1024` are well known and standard services, for example:
- `80` - HTTP
- `443` - HTTPS
- `20` TCP File transfer
- `22` TCP SSH etc

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

![[Pasted image 20240320151828.png | center |300]]


###### Setting up UDP Connection
This is a much more simple process than a TCP connection
![[Pasted image 20240320152046.png | center ]]

#### Unix Socket
Also known as the Unix domain socket. Using TCP and UDP for IPC is overkill as they are designed for seperate machines to communicate. 

Compared with an internet socket
- Uses a file system path as an address
- Limited to communication on the same machine
- Generally has lower overhead and higher performance

Has similar setup process to a TCP socket. 

Receiver Side
```python
socket_file = "/tmp/socket_example"

# Create a unix domain socket
server_socket = socket.socket(socket.AF_UNIX, socket.SOCK_STREAM)

# Bind the socket to the filepath 
server_socket.bind(socket.file)

# Listen for incoming connnections
server_socket.listen(1)
print(f"Server listening on {socket_file}")

while True:
	# Accept a connection
	connection, client_address = server_socket.accept()
	print(f"Connection from {client_address}")
	
	try:
		# Receive data from the client
		data = connection.recv(1024)
		print(f"Received data from client {data.decode()}")
```

Sender Side:
```python
socket_file = "/tmp/socket_example"

# Create a unix domain socket
client_socket = socket.socket(socket.AF_UNIX, socket.SOCK_STREAM)

try: 
	client_socket.connect(socket_file)

	# Send the data to the server
	message = "Hello from the client!"
	client_socket.sendall(message.encode())
	print(f"Sent Data: {message}")
```

### Signals
Signals are used to notify a process that a particular event has occurred. 

For each process there are default signal handlers. A process may override them if it chooses to. 

```python
import signal
import time
import sys

def signal_handler(sig, frame):
	print('Signal handler called with signal', sig)
	sys.exit(0)

# Register the signal handler
signal.signal(signal.SIGINT, signal_handler)

print("Press Ctrl+C to trigger the signal handler.");

while True:
	sleep(1)
```

#### Common signals
- `SIGABRT` - Abort function is called
- `SIGFPE` - Floating point exception
- `SIGINT` - Interrupt, interactive attention request sent to the program, most common from `^c`
- `SIGSEGV` - [[02 C Programming#Segment Fault|Segment Fault]]
- `SIGTERM` - Termination Request
- `SIGKILL` - Force Kill

#### Sending signals to a process
In C use the `kill(pid, signal)` function. 

In the Linux shell you can use `kill signal pid` to send a signal to a process. 

For example, `kill -SIGTERM 1432` sends a termination signal to a process with PID 1432

The process can ignore the signals, except the kill signal. 

### Memory Sharing 
![[Pasted image 20240320153707.png | center | 500]]
> Shared memory using memory-mapped I/O

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

#### `mmap` vs file operations
`mmap` has several advantages over file operations. 
- `mmap` allows random access, while `fread`/`fwrite` is good for sequential access
- `mmap` can load super large files. 


#### MMAP IPC
To share memory between two processes:
- Create a shared memory object file using `shm_open()`
- Map the same object file to both processes using `mmap()`

```c
int shm_fd = shm_open(name, O_CREATE | O_RDWR, 0666);
mmap(0, SIZE, PROT_READ | PROT_WRITE, MAP_SHARED, shm_fd, 0);
```