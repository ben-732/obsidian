It is important to write readable code 
- minimizes the errors
- Updating and maintenance will become much easier. 

### Structure of code 
- Title Block
	- The name, date and author information in a comment
- Include section
	- Including the required header files
- Macro Section
	- Using macros to make code more readable
	- Gets replaced by the pre-processor
- Function Prototypes:
	- Use functions where needed to make a modular program
- Main function:
	- Setup section
	- Infinite loop

### Avoid Magic numbers
Do `PORTC = PORTC | (1 << PC5)` instead of `PORTC = PORTC | 0x20`
`PC5 = 5` - defined in io header file etc etc

>[!Example] Setting a pin on Port B
>The code to set bit 6 of PORT B
>```C
PORTB = PORTB | (1 << PB6)
>```
>Or Even Better
>```C
>#define SET_PB6  PORTB |=(1<<PB6)
>```

>[!Example] Clearing a pin on Port D
>The code to clear bit 4 of PORT D
>
>```C
#include <avr/io.h>
>
>// Macros:
 >#define SET_PD4          PORTD |= (1 << PD4)
 > #define CLEAR_PD4        PORTD &= ~(1<<PD4)
 > #define PINB0_IS_HIGH    PINB && (1 << PINB0)
 >```

>[!Example] Checking if a pin on port C is set as output
>Code to check if bit 3 of DDRC is high
>
>```C
>// Macros:
 >#define DDC3_IS_HIGH    DDRC & (1 << DDC3)
// body
if(DDC3_IS_HIGH)
 >```

>[!Example] Checking if an input pin on port B is cleared
>Code to check if bit 2 if PIN B is low
>
>```C
// Macros:
#define PINB2_IS_LOW   ~PINB & (1 << PINB2)
// body
if(PINB2_IS_LOW)
>```

## Delay
We often need to set the output and change it after a specific time.  
To do this we use the `_delay_ms()` and  `_delay_us()` functions. They take a number as an argument,

When using these functions you also need to set the MCU frequency with `#define F_CPU`

```C
// Include the delay util functions
#include <util/delay.h>

#define F_CPU   16000000UL //16MHZ Crystal
```


This works well but it just wastes time on the microcontroller, nothing else can happen during this delay


>[!Example]- Example: Making a Sequence on Port D LEDs
>All the 8 pins on port D are connected to LEDS. We want to make a sequence to turn the LEDs on/off:
>- All the LEDs turn on for 500ms
>- The lower four LEDs turn on for 500ms
>- The upper LEDS turn on for 500ms
>- All LEDS off for 100ms
>```C
> #define F_CPU 16000000UL
>
#include <avr/io.h>
#include <util/delay.h>
>
>int main(void) 
>{
> // Set port D as output
>	DDRD = 0xFF; //0b11111111
>	
>	while(1)
>	{
>		// All LEDs are on
>		PORTD = 0xFF;
>		_delay_ms(500);
>		
>		// Only the lower 4 LEDs are on
>		PORTD = 0x0F;
>		_delay_ms(500);
>		
>		// Only the upper 4 LEDs are on
>		PORTD = 0xF0;
>		_delay_ms(500);
>		
>		// All 4 LEDs are off
>		PORTD = 0x00;
>	
>	}
>}
>```







