### Delay Modelling
Sometimes modelling propagation delays is required for digital circuits simulation. this delay is specified with the `after` keyword and delay expression in the signal assignment statements. 
```vhdl
signal_name <= value_expression after time_expression;
```
>[!note]
>Delay-expressions are only used for simulation in order to simulate the effects of different delays in the circuit (if necessary) and mainly for writing test benches. Delay-expressions are ignored by the synthesis tools as they assign the real delayed based on the target implementation technology. 

In VHDL three delay models can be specified

**Inertial delay model** Represents the certain amount of intertia of the signal in responding the input changes (ie.  the delay of switching devices)

**Transport delay model** Represents the transport delays (ie. in wires) in response to input changes

**Delta delays** Represents a very small delay (infinitesimally small) required for a change to occur on signals. This does not affect the simulation time. - Used only to enforce dependencies between events and ensure correct simulation

#### CMOS Delay example
![[Pasted image 20240301094328.png | center | 400]]
Charging and discharging of the load capacitance results in a *non-zero* propagation delay. 
$T_{PHL}$ the Propagation delay for High to Low
$T_{PLH}$ the Propagation delay for Low to High

#### Intertial Delay model
![[Pasted image 20240301094903.png | center | 500]]

#### Transport delay model
![[Pasted image 20240301094924.png | center | 500]]
This will not be discussed, is not normally used. 

#### Delta delay model
When a event is detected (a change from 0 to 1) a re evaluation is triggered for that distinct new state after delta time. 
![[Pasted image 20240301095701.png | center | 500]]

However, $\Delta$ is extremely small and doesn't show in the waveform
![[Pasted image 20240301095744.png | center | 500]]



### Examples
![[Pasted image 20240306082354.png | center  | 500]]
