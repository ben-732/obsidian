The ATmega328P Analogue to Digital Conversion unit has:
- 10 bit resolution
- 6 multiplexed input channels
	- 8 multiplexed input channels in `TQFP` and `VQFN`
- 0 - VCC ADC Input voltage range
- Selectable 1.1V ADC reference voltage
- Temperature sensor input channel
- Interrupt on ADC conversion complete

Reference voltage given to `AREF` pin 

#### Input
- The ADC is connected to an 8 channel analogue multiplexer
- It allows eight single ended voltage inputs from the pins of ATmega328P
	- The single ended voltage inputs refer to 0v (ground)
- The selection of input channel is through ADC multiplexer selection register `ADMUX`
![[Pasted image 20230602094448.png | center | 600]]

The channel selection is done through the bits `MUX3:0`
![[Pasted image 20230602094711.png | center | 600]]

#### Reference Voltage (VREF)
Indicating the conversion range for the ADC. The `REFS[1:0]` bits in the `ADMUX` register select the voltage reference for the ADC. 
- The voltage reference can be set to `AVCC` which is equal to the VCC voltage value
- The voltage reference could be set to `AREF`. The voltage on the externally provided `AREF` pin
- Internal 1.1v voltage can be used as the reference

![[Pasted image 20230602095044.png | center | 600]]

#### Conversion
- An n-bit ADC Converts a voltage linearly between `GND` and `VREF` in $2^n$ steps (LSB)
- The lowest code is read as 0
- The highest code is read as $2^{n}-1$
- Any value between 0V and  `VREF` takes a number between $0\to2^{n}-1$
- The result is defined by the equation:
$$
ADC = \frac{{V_{in}\times 2^n}}{V_{REF}}
$$
$$
\text{Resolution} = \frac{V_{REF}}{2^n}(\text{in V or mV})
$$

- The n-bit digital result from ADC is quantized since it can only take discrete values
- A range of input voltages (1 LSB wide) will code to the same value.
- The quantization error is between -0.5LSB and 0.5LSB

![[Pasted image 20230602100430.png | center | 600]]

#### Conversion Clock
The conversion circuitry requires an input clock frequency between  50kHz and 200kHz to get a maximum resolution. The ADC module contains a prescaler which generates an acceptable ADC clock frequency

The prescaling is set by the `ADPS` bits in `ADSCRA` register

![[Pasted image 20230602100748.png | center | 600]]

- ADC Enable bit `ADEN` Enables / Disables ADC
- ADC Start Conversion `ADSC` Bit starts conversion
- ADC Interrupt flag `ADIF` is set to 1 when ADC conversion is complete
- ADC interrupt enable `ADIE` bit enables the ADC conversion interrupt
- ADC Prescaler select bits `ADPS[2:0]` select the value of the prescaler

#### Example?
- Choosing the correct channel `ADMUX |= (1 << MUX2);`
- Choosing the correct reference voltage `ADMUX |= (1 << REFS0) | (1 << REFS1);`
- Enabling the ADC: `ADCSRA |= (1<<ADEN)`
- Setting the Prescaler: `ADCSRA |= (1 << ADPS2) | (1 << ADPS1) | (1 << ADPS0)`
- Enabling the interrupt: `ADCSRA |= (1 << ADIE)`

