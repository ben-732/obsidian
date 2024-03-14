## Polling
Microcontrollers are used in many applications where responsiveness is crucial. They need to be checking the status of input signals and then reacting to them.

One approach to doing this is to repeatedly check the values of the input pins. We can then detect change in this value and take action based on this change

>[!info] Polling
>The regular checking of the desired information from the source

This has the downside of being able to miss events if they last a very short amount of time, they happen entirely in the gap between an input being polled twice. 

This approach has some critical issues with both microcontroller performance and power efficiency. When it is constantly checking inputs for events, it cannot do other tasks and it also cannot rest, draining the batteries faster. 


## Interrupts
The program runs as usual but with the possibility of being interrupted from a source. 

When this happens, it briefly stops running the main program, executes the action associated with the interrupt then continues with running the program. 

More specifically:
![[microcontroller interupt process.png|center|400]]
Interrupts are turned off while executing an interrupt


### Interrupt service routine (ISR)

- Interrupt handlers aka interrupt service routines are software routines that are invoked by hardware in response to an interrupt. 
- When an interrupt is triggered, the source of the interrupt is checked and the corresponding ISR is called
- Each interrupt event results in the run of its own ISR. 

> There should be a mechanism to find the proper ISR based on the source of interrupt

Pointers to functions are stored in register

### Interrupt Vectors
Each interrupt is assigned a number, 
Interrupts with lower numbers have higher priority

Each interrupt is assigned a specific interrupt vector. Interrupt vectors are addresses that inform the processor where to find the ISR. 

>[!info] Interrupt Vector
>The interrupt vector is an address, stored as data, located at a particular memory location, which points to where the interrupt service routine can be found. 

Extensive processing should not be preformed inside an ISR. This could impact the functionality of the main program. 

It might contain a few instructions that do something like set a global variable which indicates that an event has occurred. (A variable is set or incremented)

The variables that are used inside an ISR should be defined as global variables

The variables used inside an ISR should be defined as **volatile** variables

>[!info] Volatile Variables
The volatile keyword for variables ensures that these variables are not optimised by the compiler. It tells the compiler that these variables can change at any time during the execution. 

### AVR Interrupts
Available interrupts in ATmega328P

**External interrupts:** Detects changes on `INT0` and `INT1` pins

**Pin Change Interrupts:** Detects changes on any of the 23 I/O Pins

**Watchdog time-out Interrupt:** Notifies that the watchdog timer has timed out. (A timer that is used to prevent system hangup)

**Timer/Counter Interrupts:** Notifies a few different events on three timers `timer0`, `timer1`, `timer2`

**ADC Interrupts:** Notifies the completion of an analog to digital conversion. 

**Serial Interface Interrupts:** Notifies important events on the transmission and reception of serial data. 

#### AVR Interrupt Vectors
![[AVR Interrupt Vectors.png |centre |600 ]]

![[AVR Interrupt Vectors 2.png| center | 600]]


### Writing the ISR
We write the interrupt service routines using `isr()`. We can pass the interrupt vector of any specific interrupt to ISR(). The address of interrupt service routines will be stored in the interrupt vector. 

`ISR()` is a macros defined in `avr/interrupt.h` to make our job easier in writing interrupt service routines. 
`ISR` gets a vector as the input which is the interrupt vector. It makes the address of created ISR equal as the address stored by the interrupt vector

```c
#include <avr/interrupt.h>
#include <avr/io.h>

ISR(INT0_vect)
{
	// Code for handling external interrupt 0
}
```

The names for the interrupt vectors are included in the `avr/io.h` header file. 

>[!info] Interrupt Vectors in C
>![[Pasted image 20230508122212.png | center]]

>[!example] Toggling an orange LED on Pin B6. 
>When timer 1 overflows, toggle the orange LED on port B pin 6
>```C
#define TOGGLE_ORANGE_LED   PINB = (1 << PB6)
>
>ISR(TIMER1_OVF_vect)
>{
>	TOGGLE_ORANGE_LED;
>}
>// TOGGLE_ORANGE_LED can also be written as
>#define TOGGLE_ORANGE_LED PORTB ∧= (1<<PB6)
>```

>[!example] Turning on a red LED on interrupt
>When the ADC is complete, switch the red LED on port **c** bit **2** on. 
>```c
#define TURN_RED_ON PORTC |= (1<<PC2)
>
>ISR(ADC_vect)
>{
>	TURN_RED_ON;
>}
>```

### External Interrupts
Can be triggered by:
- `int0` and `int 1` pins
- Any of the pins `PCINT0` to `PCINT23`
- The external interrupts on `INT0` and `INT1` can be triggered by a **Falling**o or **Rising edge**, or a **Low level**.
- There are three pin change interrupts `PCI0`, `PIC1`, `PCI2` that will trigger when a pin change occurs
	- `PCI0` will trigger when enabled `PCINT[7:0]` pin toggles
	- `PCI1` will trigger when enabled `PCINT[14:8]` pin toggles
	- `PCI2` will trigger when enabled `PCINT[23:16]` pin toggles

#### Global Interrupt Enable
The status register has a bit that is related to interrupts.
Bit 7 in `SREG` is `I: Global Interrupt Enable`. This bit must be set for **all** interrupts to be enabled. 

![[SREG - AVR Status Register.png| center| 500]]

This `I bit` is cleared by hardware after an interrupt occurs so we don't get another interrupt while executing the first one. 

At the end of the ISR the `I bit` is set again. This is done by a `RETI` instruction (Return from interrupt) which will always be the last instruction in the ISR

We additionally need to set the `I bit` in our code. Instead of writing directly to the `SREG` Register, we can use the `sei()` function. This function is the `avr/interrupt.h` header file. 

#### External interrupts mask register
This register is used to enable / disable interrupts on `INT0` and `INT1` pins. 

When the `INT0` and or `INT1` bits are set, external interrupts on these pins are enabled
![[External Interrupt Mask Register.png | centre | 600]]

#### External interrupts control register
Contains the control bits for interrupt sense control. 
- bits `0` and `1` are for `int0`
- bits `2` and `3` are for `int1`

![[External interrupt control register.png | center | 600]]

By setting the values of the two bits you can control trigger for the external interrupt pin
![[External interrupt register binary values,,,.png | center  | 600]]

#### Flag Register
The external interrupt flag register (EIFR) is used to detect eh occurrence of INT0 and INT1 interrupts. 

These pins become logic 1 when the interrupt is triggered on the corresponding pin. 
![[EIFR Register.png | center | 600]]

There is normally no reason for us to have to check this pin, the hardware handles calling the interrupt function for us. 

If the flag becomes one from one, the MCU jumps to the corresponding interrupt vector
- The flag is cleared when the interrupt routine is executed
- The flag can also be cleared by writing a logical one to it. 


>[!Example] Detecting Rising edge on INT1
>C code to enable the external interrupt on `INT1` pin to detect the rising edge
>
> The order of these lines is kinda important
>```C
>EICRA |= (1 << ISC10);   // Detecting Rising edge
>EICRA |= (1 << ISC11);   // Detecting Rising edge
>EIMSK |= (1 << INT1);  // Enable INT1 Interrupt
>sei();   //Enable global interrupts
>```


>[!example] `INT0` Interrupt
>A digital input signal coming from a sensor is connected to `INT0` pin. An LED is connected to bit 0 of port B. Turn on the LED every time `INT0` goes from 1 to 0. 
>```C
>#include <avr/io.h>
>#include <avr/interrupt.h>
>#include <util/delay.h>
>
>#define TIME_DELAY      30
>
>#define CONF_PBO_OUTPUT   DDRB |= (1 << DDB0)
>#define SET_LED           PORTB |= (1 << PB0)
>#define CLEAR_LED         PORTB &= ~(1 << PB0)
>
>ISR(INT0_vect)
>{
>	SET_LED;
>}
>
>int function main(void)
>{
>	EICRA &= ~(1<< ISC00);   // Detecting Falling edge
>	EICRA |= (1 << ISC01);   // Detecting Falling edge
>	EIMSK |= (1 << INT0);    // Enable INT1 Interrupt
>	sei();                   //Enable global interrupts
>
>	while(1)
>	{
>		CLEAR_LED;
>		_delay_ms(TIME_DELAY);
>	}
>}
>
>```



### Pin Change Interrupts  
#### Control Register `PCICR`
Enables/Disable interrupts on `PCIE0`, `1`, `2`
![[Pin Change Interrupt control register.png| center | 500]]

When the corresponding bit on the register is set, any pin change in the range will trigger the interrupt:
- `PCIE0` $\to$ `PCINT[7:0]`
- `PCIE1` $\to$ `PCINT[14:8]`
- `PCIE2` $\to$ `PCINT[23:16]`

#### Pin Change Interrupt Flag Register
Flag register like for external interrupts, one for each group, `PCIE` interrupt. 


#### Pin Change Interrupt Mask Registers
The pins in the three groups (`PCINT[7:0]`, `PCINT[14:8]`, `PCINT[23:16]`) are individually enabled by the `PCMSK0`, `PCMSK1`, and `PCMSK2` registers

![[Pin Change Interrupt Mask Registers.png | center | 500]]


#### Examples
>[!example] Pin Change Interrupt
> Count the number of times pin PB6 is changed. Display this on 8 LEDs connected to port D
> ```C
>#define F_CPU 16000000UL
>
>#define <avr/io.h>
>#define <avr/interrupt.h>
>#define <util/delay.h>
>
>#define CONF_PB6_INPUT    DDRB &= ~(1<<DDB6)
>#define CONF_PORTD_OUTPUT DDRD = 0xFF
>
>volatile uint8_t count = 0;
>
>
>// ----- ISR -----
> ISR(PCINT0_vect)
> {
> 	count++;
> }
>
>int main(void)
>{
>	CONF_PB6_INPUT;  // Set Pin B6 as input
>	CONF_PORTD_OUTPUT;  // Set all port D as output.
>	
>	
>	PCICR |= (1 << PCIE0); // Enable PICE0 Interrupt
>	PCMSK0 |= (1 << PCINT6);  // Enable PCINT6 in mask (Port B Pin 6)
>	sei();
>	
>	while(1)
>	{
>		PORTD = count;
>	}
>}
>```






