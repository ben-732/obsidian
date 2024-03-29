- Different types os Serial Communication
- ATmega328P Serial Interfaces
- Serial Peripheral Interface (SPI)
- UART
- Two wire

An alternative to parallel interfaces, serial is slower but greatly simplifies the hardware required for communication between devices. You can send more information with less pins but over a span of time. 

#### Modes of transmission
![[Types of serial communication.png| center | 400]]

Simplex is one way, Half duplex is two way communication but over only one wire. 

## Serial Interfaces
- Serial Peripheral interface (SPI)
	- Full Duplex
	- Synchronous (Clock involved)
- Universal Synchronous Asynchronous Receiver and Transmitter (USART)
	- Full Duplex
	- Either synchronous or asynchronous
- Two Wire Serial (TWI)
	- Half-duplex 
	- Synchronous

### SPI
A full duplex three-wire data transfer
Allows high-speed synchronous data transfer between the ATmega328P and peripheral devices or other AVR devices
![[Pasted image 20230529121855.png | center | 500]]
$MOSI$ - Master out slave in
$MISO$ - Master in slave out
$SCK$ - Clock signal
$SS$  - slave select / enable lines. 

The master initiates communication cycle by pulling low the slave select ($\overline{SS}$) pin of the desired slave
Master and Slave prepare the data to be sent in their respective shift registers
Data is sent between the two on their $MISO$  and $MOSI$ lines
After each data packet, the master synchronises the slave by pulling the $\overline{SS}$ line high
![[Pasted image 20230529122914.png | center | 500]]

### USART
- Full duplex operation. 
- Asynchronous or synchronous so the clock can be generated by either master or slave. 
- High Resolution Baud Rate generator
- Supports serial frames of 5, 6, 7, 8, or 9 Data bits with 1, or 2 stop bits
- Odd or even parity generation
- Parity check and frame error detection. 
- Three seperate interrupts:
	- TX Complete
	- TX Data Register Empty
	- RX Complete

The interface requires three lines
- `TxD` Transmit Data
- `RxD` Receive Data
- `XCK` Clock
	- Used in synchronous operations to synchronise the data transfer

#### Frame Format
A serial frame is defined as:
- One character of data bits
- Synchronisation bits (Start and stop bits)
- Optionally a parity checking bit
![[Pasted image 20230529123448.png | center | 500]]

The USART accepts all 30 combinations as valid frame formats
- 1 Start bit
- 5, 6, 7, 8, or 9 data bits
- no, even or odd parity bits
- 1 or two stop bits

#### Parity Bit
- The parity bit is calculated by y doing an exclusive-or (`XOR`) of all the data bits.
- The parity bit is located between the last data bit ad first stop bit of a serial frame. 
- Using the parity bit helps in detecting transmission error
- If even parity is used, the result of the exclusive-or is used. 
	- $p_{even} = d_{n-1}\oplus d_{2} \oplus d_{1} \oplus 0$
- If odd parity bit is used, the result of the exclusive-or is inverted
	- $p_{odd} = d_{n-1}\oplus d_{2} \oplus d_{1} \oplus 1$

#### Configuration / Initialisation
- The USART has to be initialised before any communication takes place
- The initialisation process consists of 
	- Setting the baud rate
	- Setting the frame format
	- Enabling the transmitter or the receiver
- Three different interrupts can be generated by the USART
	- TX complete Interrupt shows the data transmit is completed.  
	- RX complete Interrupt shows the data receive is completed.  
	- TX Data Register Empty shows that there is no more data is the  transmit register.  

### TWI
- The 2-wire serial interface **TWI** is a simple yet powerful and flexible communication protocol. 
- TWI is a synchronous half duplex interface

![[Pasted image 20230529124355.png | center | 500]]

- The protocol allows to interconnect up to 128 devices using only two bi-directional bus lines 
	- `SDA` serial data line
	- `SCL` Serial clock line
- All devices connected to the bus have individual addresses
- The devices that initiates and terminates a transmission is the **master**
	- The master also generates `SCL` clock signal
- The device addressed by a master is a slave
- The device placing data on the bus is the transmitter
- The device receiving data off the bus is the receiver

#### Data Transmission
- The master initiates and terminates the data transmission
	- The transmission is initiated when Master issues a **START** condition on the bus
	- And terminated with **STOP** condition. 
- All address packets sent on the TWI bus are 9 bits long:
	- 7 Address bits
	- 1 Read/Write control bit
	- An Acknowledge bit

![[Pasted image 20230529124836.png | center | 500]]

All Data packets transmitted on the TWI bus are 9 bits long consisting of 
 - One data **Byte** (8 bits)
 - An acknowledge bit

An acknowledge bit is signalled by the receiver pulling the `SDA` line LOW during the 9th `SCL` cycle
![[Pasted image 20230529125027.png | center | 500]]

#### Typical Transmission
![[Pasted image 20230529125055.png]]

The TWI interface in ATmega328P supports both Master and Slave Operations.  
- Both Master and Slave can be Transmitter or Receiver.  
- Different functionalities are configured by using the related registers.  
