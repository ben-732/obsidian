## VGA Interface
VGA (Video Graphics Array) is a popular display standard developed by IBM and introduced in 1987. 

The resolution can vary but a standard default is `640x480`. The screen refreshes the display from left to right, top to bottom. 

![[Pasted image 20240327084617.png | center | 400]]
Images on VGA screen are displayed by turning the pixels on and off

We are aiming to have the screen run at 60Hz to avoid flickers. 

We will use the common VGA display standard at `25MHz` pixel rate with `640x480` resolution. Each pixel takes `40ns` at `25MHz` pixel rate.

### Signals
The VGA Interface contains 5 active signals. 
- Horizontal and Vertical synchronisation signals.
- Three analog signals for red, green and blue colours

By changing he analog voltage levels of the RGB signals, different colours can be produced. Depending on the number of bits supported by the development board, different amounts of colours can be used. `8bit` colours?

#### Horizontal Synchronisation
Horizontal sync signifies the end of one row of data (i.e. 640 pixels) and the start of the next.  
- The data (RGB) inputs on the monitor must be off for a time period called the back porch (b) after the hsync pulse occurs.  
- During the data display interval (c) the RGB data drives each pixel in turn across the row being displayed.  
- When data display interval is finished, the beam returns from the right most position to left most. During the return process, no pixel data is displayed.  
- Front porch (d) is a time period where the RGB signals must again be off before the next hsync pulse can occur

![[Pasted image 20240327085251.png | center | 500]]

Pixels per section, times by `40ns` to get time. 
- **Horizontal sync** (a) corresponds to 96 pixels.  
- **Back porch** (b) corresponds to 48 pixels.  
- **Display interval** (c) corresponds to 640 pixels.  
- **Front porch** (d) corresponds to 16 pixels.

![[Pasted image 20240327085715.png | center | 600]]
#### Vertical Synchronisation
Vertical sync pulse signifies the end of one frame and the start of the next, and the data refers to the set of rows in the frame.  
- **Vertical sync** (a) corresponds to 2 lines.  
- **Back porch** (b) corresponds to 33 lines.  
- **Display interval** (c) corresponds to 480 lines.  
- **Front porch** (d) corresponds to 10 lines.

![[Pasted image 20240327085848.png | center | 500]]
Referred to in terms of lines rather than pixels. 

![[Pasted image 20240327085559.png | center | 400]]

### DE0-CV Board Interface
RGB Signals are analogue signals and have resisters attached to enable modifying the levels of red, green and blue. 
![[Pasted image 20240327085939.png | center | 500]]

### VGA Sync Component
Supplied for project, need to provide inputs and ouputs. 

We need a component to drive the control signals to the display and provide pixel values at the right rate.  
- In order to generate the VGA signal at 25 MHz, the clock signal provided by DE0-CV (50MHz) needs to be halved.   - Should use something called a PLL?
- 25 MHz clock signal can be used by counters to generate the horizontal and vertical sync signals.  
- The counters also represent row and column address of a pixel, which can be used by other components to retrieve pixel information.


### Text Display
A group of characters are stored in a memory block in the FPGA. This memory is instantiated in the char_rom.vhd

The memory should be initialized with the information of character patterns.

- A \*.mif file is used to initialize the memory.
- TCGROM.mif is the memory initialization file that contains the patterns of 64 characters.
- Each character in a .mif file is described through 8 lines of memory address and is translated to a block of 8x8 pixels.

ROM used as the values are not needed to be changed. 

.mif examples
```mif
Depth = 512 %How many lines of data%
width = 8 %How many bits per line%
Address_radix = oct; % format of addresses%
data_radix = bin; % format of data %

Content
	Begin
000   : 00111100 ;
001   : 01100110 ; 
002   : 01101110 ; 
003   : 00111100 ; 
004   : 00111100 ; 
005   : 00111100 ; 
006   : 00111100 ; 
007   : 00111100 ; 
010   : 00111100 ; 
.....
```


## PS/2 Mouse Interface
The PS/2 device interface was originally developed by IBM and is used by many mice and keyboards. The PS/2 mouse and keyboard implement a bidirectional synchronous serial protocol. The interface consists of two bidirectional signals: Clock, and Data. 

![[Pasted image 20240417092351.png | center | 300]]

### Bus states
There are three different bus states:
**Idle**
- Both Data and Clock lines are kept high.
- This is the only state where the mouse is allowed to begin transmitting data.

**Inhibit**
- The host may inhibit communication at any time by pulling the Clock line low.
- The host then pulls Data low and releases Clock to enter Host Request-to-Send

**Host Request-to-Send**
- This state signals the device to start generating clock pulses for the host to device communication.

The clock signal is always generated by the mouse, unless the host wants to inhibit mouse transmission in order to send a command

### Data Transmission
Data is transmitted one byte at a time. Each byte is sent in a sequence consisting of 11-12 bits. These bits are:

- 1 start bit: Always ’0’
- 8 data bits: Least significant bit first.
- 1 parity bit: Odd parity
- 1 stop bit: Always ’1’
- 1 acknowledge bit: Host-to-device communication only

![[Pasted image 20240417092941.png | center | 350]]

Host (DE0-CV board) drives the data line when sending commands to the mouse.
Mouse drives the data line when sending data to the DE0-CV board.

#### Host to Device Communication
The clock signal is always generated by the mouse unless in Inhibit state.  

Data is shifted out by the host at clock falling edge and is read by the mouse at clock rising edge.

An acknowledgment bit is sent from the mouse to the host

![[Pasted image 20240417093215.png | center | 400]]

The diagram shows the separated clock and data signals generated by host and mouse side which can be superimposed

The steps that must be followed in order to send data to device:
1. Bring the Clock line low for at least 100 microseconds.
2. Bring the Data line low.
3. Release the Clock line.
4. Wait for the mouse to bring the Clock line low.
5. Set/reset the Data line to send the first data bit. (LSB)
6. Wait for the mouse to bring Clock high.
7. Wait for the mouse to bring Clock low.
8. Repeat steps 5-7 for the other seven data bits and the parity bit.
9. Release the Data line.
10. Wait for the mouse to bring Data low. (Ack)
11. Wait for the mouse to bring Clock low.
12. Wait for the mouse to release Data and Clock.

#### Device to Host Communication
- The clock signal is generated by the mouse.  
- The data bit is written by the mouse at clock rising edge and is read by the host at clock falling edge. No acknowledgment bit is required.
- No acknowledgment bit is required.

##### Mouse Modes
- **Reset Mode:** Initialisation and self test is done. 
- **Stream Mode:** Mouse transmits three data packets. Host must transmit 0xF4 command to the mouse to initiate data reporting.
- **Remote Mode:** Host requests movement data packets. 
- **Wrap Mode:** This is diagnostic mode

##### Initialisation
At power up, the mouse will go to Reset Mode.

It runs a self test and sends the message 0xAA, which indicates that the test is passed.

It then sends 0x00 which is the ID for standard PS/2 mouse. If the PS/2 mouse is connected before the FPGA device is programmed this data can be ignored in the interface design.

##### Stream Mode
In Stream Mode, the streaming should be enabled by a command from host.
  - Command 0xF4 is sent by the host.
  - Acknowledge is sent by the mouse.
  - Mouse continuously transmits three data packets which include the movement information as well as the pushbutton status.
- Mouse sends data at a default sampling rate of 100 samples per second

Data packet format:
![[Pasted image 20240417094210.png | center | 500]]
`X7-X0`: Moving distance of X-axis (horizontal move).
- ▶  moving right = positive values
- ▶  moving left = negative values

`Y7-Y0`: Moving distance of Y-axis (vertical move).
- Moving up = positive values
- Moving down = negative values

`Xo`: X data overflow bit (1 = overflow) 
`Yo`: Y data overflow bit (1 = overflow)
`Xs`: X data sign bit (1 = negative)  
`Ys`: Y data sign bit (1 = negative)
`L`: Left button status bit (1 = button pressed, 0 = released)
`R`: Right button status bit (1 = button pressed, 0 = released)


Byte 1 of the packet contains button status information and X, Y motion sign, and overflow bits.
-  8-bit distance value together with the sign bit covers the range of -256 to +255.
- If the movement is too fast the overflow bits are set. Byte 2 and 3 contain the value of relative X, Y movement.
- These values should be added to the current cursor position.
- Two 10-bit registers are used to store the cursor position within the screen range of 640x480.
- The registers should be updated once a new packet of data is received.

The cursor is usually initialised to the center of a video screen at power up.

## LFSRs
Linear Feedback shift registers are shift register whose input bit is the output of a linear function of two or more bits of its previous states.

The linear feedback can be formed by performing exclusive-OR on the outputs of two or more of the flip flops together. Alternatively XNOR can be used for the feedback.

LFSRs can be used in variety of applications such as
- Pseudo-random number generators
- Test pattern generation
- Cyclic Redundancy Check (CRC)
- Cryptography
![[Pasted image 20240417094952.png | center | 400]]

The points within the register chain, where the feedback comes from are called taps.
- Taps are the bits that influence the output.
- Two LFSRs with the same seed but different taps generate different sequences.


The initial value of the LFSR is called the seed. It should be a non-zero value, otherwise LFSR would be stuck at the seed value.

An LFSR is of maximal length if it sequence through every possible value.
- A maximal length n-bit LFSR can sequence through $2^n-1$ values.
- The state "0000. . . " (all zeros) is not included in the sequence.


The choice of taps determines how many values there are in a given sequence before the sequence is repeated. Some tap choices for maximal length sequence is provided.
![[Pasted image 20240417095356.png | center | 300]]

There are two types of LFSRs, depending on how feedback is formed:

In **Fibonacci LFSR**, the XOR gates (taps), are placed on the feedback path.

Increasing the levels of logic in the combinational feedback path can negatively impact the maximum clocking frequency of the function.

![[Pasted image 20240417095519.png | center | 500]]

In **Galois LFSR**, the XOR gates (taps), are placed between the registers. Galois type is more recommended in this project. This is preferred

![[Pasted image 20240417095459.png | center | 500]]