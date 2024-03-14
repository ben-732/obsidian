- Three timers in this microcontroller `timer0`,  `timer1` and `timer2`
- Work in parallel with the CPU 
- Provide time measurement features without stalling execution of the main program
- `_delay_ms()` stalls the execution of the processor and impacts performance

### Timer/Counter 0
A general purpose 8 Bit timer/counter module

It has two independent output compare units
- Output compare units make a comparison between the value of the timer/counter and the values store in their compare register `OCR0A` and `OCR0B`
- When the timer/counter value is equal to the value inside the output compare registers, the result is reflected on the output compare pins `OC0A` and `OC0B`

The timer has three independent interrupt sources
- `TOV0` - When the timer/counter overflows
- `OCF0A` - When the value of the timer/counter equals `OCR0A`
- `OCF0B` - When the value of the timer/counter equals `OCR0B`

The comparison results can be used by waveform generator units to generate PWM output on `OC0A` and `OCOB`
- PWM signals are used to change the average value of the signal by changing its duty cycle
- PWM signals are used in power regulation, rectification and DAC applications

It is possible to make `timer0` to count between 0 and the value in `OCR0A`

>[!info]- `timer0` Block diagram
>
![[Pasted image 20230517230524.png]]

#### Control
Depending on the mode of operation, the counter is cleared, incremented, or decremented at each timer clock ($clk_{T0}$)
This can be generated from an external or internal clock source. 
- Clock source can is selected by clock select bits `CS02:0`
- The external clock is sourced from `T0` pin
- The internal clock can be used directly or via the pre-scalers. 
- When no clock source is selected, the timer is stopped. 

The timer/counter overflow flag `TOV0` is set according to the mode of operation

![[Pasted image 20230517231536.png]]

Control Logic:
- Count - Signal increment or decrement the `TCNT0` by 1
- Direction - Signal selects between the incrementing and decrementing
- Clear - signal clears `TCNT0` by setting all bits to zero
- Top  - signal notifies that `TCNT0` has reached maximum value
- Bottom - Signal notifies that `TCNT0` has reached minimum value (zero)

![[Pasted image 20230517232031.png]]

#### Compare Units
The 8-bit comparator continuously compares `TCNT0` with the output compare registers

When `TCNT0` equals one of the output compare registers, the comparator signals a match. 
A match will set the output compare flag at the next timer clock cycle
- If the corresponding interrupt is enabled, the output compare flag generates an output compare interrupt.

Both `OCR0A` and `OCR0B` are accessible by the software and can be configured to any value. 
- This feature provides accurate timing and event management capability
- Timer/Counter modules are separated hardware that work in parallel with the processor
- We can avoid delay instructions in the program and improve the performance of program execution. 

#### Operation Modes
The timer can operate in different modes
- The behaviour of the counter and the output compare pins depends on the mode of operation.

The mode is defined by the combination of:
- Waveform Generation mode `WGM02:0` 
- Compare output Mode `COM0x1:0` bits:
	- `COM0A1:0` 
	- `COM0B1:0` 
Timer/Counter operation modes are:
- Normal Mode (`0` to `255`)
- Clear timer on Compare Match mode (CTC) (0 to `OCR0A` )
- Fast PWM mode
- Phase Correct PWM Mode

##### Normal Mode
The simplest mode of operation is normal mode
- Counting direction is always up. The counter is incremented at each timer clock cycle
- The counter overruns when it passes the maximum 8-bit value **TOP** 0xFF
- It then restarts from **Bottom** 0x00
- Timer/Counter overflow flag `TOV0` is set in the same timer clock cycle that `TCNT0` becomes zero
- Output compare unit can be used to generate interrupts for any specific time
- It is nor recommended to generate waveforms in this mode

##### CTC Mode
Clear Timer on Compare Mode
- The counter is cleared when the counter value matches `OCR0A`
- This mode provides control over the frequency of the compare match output
- An interrupt can be generated each time the counter reaches the TOP value. 

##### Fast PWM Mode
For generating high frequency PWN waveforms
The counter counts from **Bottom** to **Top**, then restarts from **Bottom**
**Top** is defined as either `255` or `OCR0A` depending on the `WGM02:0` bits 
In non-inverting clear mode, the output compare `OC0x` is cleared on the compare match and set at the **Bottom**

![[Pasted image 20230522172013.png]]


##### Phase Correct PWM Mode
For generating a high resolution, phase correct PWM waveform.
- The counter counts from **Bottom** to **Top** then from **Top** to **Bottom** Creating a triangle shape. 
- TOP is defined by `0xFF` or `OCR0A` based on the settings in `WGM02:0`
- In non inverting compare mode, the output compare `OC0x` is cleared on compare match while counting and then set on the compare match while counting down. 

#### Timing 
`timer/counter0` is a synchronous timer. 
-  The timer clock acts as a clock enable signal.

The clock frequency can be divided by the prescaler. 
- `timer/counter0` can be set to `8`, `64`, `256`, or `1024`
- Prescalers are used when measuring longer time events are required. 
- Greater range for timing length is traded off for precision / resolution. 
>[!info]- Clock with prescaler
![[Pasted image 20230522174031.png]]

#### `timer/counter0` Registers
`TCCR0A` and `TCCR0B` are used to define the mode of operation and the value
![[Pasted image 20230522174458.png]]
##### Pinout Control 
![[Pasted image 20230522174546.png]]
##### Waveform Generation Modes
![[Pasted image 20230522175424.png]]
##### Clock Select Bits
![[Pasted image 20230522180020.png]]

#### Interrupts
`Timer/counter0` can generate three different interrupts
- Timer overflow
- Equals `OCR0A`
- Equals `OCR0B`

These need to be enabled in the `TIMSK0` Mask register
![[Pasted image 20230522183209.png]]
They have flags in the `TIFR0` Register
![[Pasted image 20230522183215.png]]


### Timer / Counter 1
16-bit timer
Input capture feature - allows you to time the duration of an input
Allows you to measure bigger values

Has two independent Output Compare Units
Shares its prescalers with `Timer/Counter0`
Has four independent interrupt sources
- `TOV1` when the timer/counter overflows
- `OCF1A` When the value of timer/counter equals the `OCR1A`
- `OCF1B` When the value of timer/counter equals the `OCR1B`
- `ICF1` When an event on `ICP1` pin triggers the capture event. 

#### Input Capture Unit
The input unit can capture external events and give them a time-stamp to indicate a time of occurrence. The time stamps can be used to calculate the frequency or the duty cycle. 

The external signal indicating the event can be applied to `ICP1` pin or it can come from the result of analog comparator unit. 

>[!info]- Input capture unit block diagram
![[Input Capture unit - Block Diagram.png | center ]]

#### `Timer/Counter1` Registers
The timer/counter register `TCNT1` and the output compare registers `OCR1A` and `OCR1B` are all 16 bit registers. However the data bus in the AVR is only 8 bit. This means that two read/write operations are required to update a 16-bit register. To avoid any mistakes between the two executions, the high byte is stored in the `TEMP` register. 

Both the higher and lower byte are updated in the same clock cycle. When writing C code, the compiler handles the 16 bit access. 

The content of Timer/Counter1 Register `TCNT1` should be copied into the Input Capture Register `ICR1` when the event triggers the input capture interrupt.  

![[Pasted image 20230522124210.png | center | 600]]

##### Compare Registers
These registers are used to define the time of events measured by the timer/counter 
![[Pasted image 20230522124351.png | center | 600]]

##### Control Registers
Used to define the mode of operation and the value of the prescaler
![[Pasted image 20230522124527.png]]
`ICNC1` When the input capture noise canceller bit is set, the noise canceller is activated. 
`ICES1` Input capture edge selector chooses which edge on the `ICP1` pin is used to trigger a capture event. `0` for falling edge, `1` for rising edge. 

![[Pasted image 20230522124618.png]]


>[!info] Compare output mode bits
![[Pasted image 20230522124838.png  ]]

>[!info] Waveform Generation mode bits
![[Pasted image 20230522124948.png ]]

>[!info] Clock Select Bits
>Used to define the source of the timer
>![[Pasted image 20230522125205.png]]

#### Interrupt Mask Register
Timer `timer/counter1` mask register `TIMSK1` is used to enable/disable timer interrupts
- `T0IE1` Overflow interrupt
- `OCIE1A`, `OCIE1B` Compare match A and B
- `ICIE1` input capture interrupt

![[Pasted image 20230522125501.png]]


### Timer/Counter 2
8-bit timer
- Two independent output compare units `OCR2A` and `OCR2B` 
	- Compare result reflected on output compare pins `OC2A` and `OC2B`
- Three Independent interrupt sources:
	- `TOV2`: When the timer/counter overflows
	- `OCF2A/B` When the value of the timer/counter equals `OCR2A/B`
- Asynchronous operation feature
	- Can be clocked from an external 32kHz watch crystal
	- It can be used as a real time counter (Counting Seconds)

Asynchronous means that things are not synced together - There are components that are running at different times to everything else. 

#### Source
Clock/timer 2 can be sourced from an external asynchronous input or internal clock source
- The external clock is sources from `TOSC1` pin
- Setting bit `AS2` in `ASSR` register selects the external clock as the clock source
- Both internal clock and the external clock share the same prescaler 
- the value of prescaler is selected by clock select bits `CS22:0`
- When no clock source is selected, the timer is stopped
![[Pasted image 20230602092032.png | center | 600]]

Asynchronous Status Register `ASSR` is used for asynchronous operation
- The `AS2` bit defaults to zero setting timer/counter2 to internal clock
	- Toggle to 1 for external clock on `TOSC1` pin
	- A 32kHz crystal should be connected between `TOSC1` and `TOSC2` pins
- If `EXCLK` external clock pin is _also_ set to one, an external clock signal can be connected to `TOSC1`
![[Pasted image 20230602092345.png | center  | 600]]

#### Prescaler
Additional options available 32 and 128



