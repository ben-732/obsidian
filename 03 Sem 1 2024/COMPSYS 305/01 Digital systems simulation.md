
A digital system is any digital circuit that processes or stores digital information. 

There is a process for designing this, 

Design idea -> Design specifications -> Functional Modelling

Model means our understanding of the system, it only represents the information and details about the system that re relevant to our design at that stage. (Unnecessary details are abstracted away).

![[Pasted image 20240228081959.png | center | 500]]

#### Timing of design process
How much design effort is required to handle complex Digital systems

Example: Design and verification of a digital circuit with 2.5 Million gates

Functional design: Estimated at 100 staff months
Logic design: 6250 Staff months
Circuit Design: 62500 Staff months

To cope with this large design complexity we need electronic design automation tools (EDA) to shorten design time and create more reliable circuits

### Hardware Description Language
Hardeware description languages are used to create a model of a complex digital system for synthesis and simulation by using EDA tools. 

**Synthesis** is the process of constructing a gate level description of the circuit from a given description for the circuit in a Hardware Description Language like VDHL based on the target device technology

**Simulation** is used to test and verify the correctness of functionality of the modelled digital system (before and after the synthesis process) based on digital design traits. 

#### Advantages of HDLs
- Capturing the system in a formal way
- Communicating the understanding of the function of the system to others
- Formal testing and verification of the system (testing correctness of design with respect to the specification)
- Automatic synthesis of the system
- Improving reliability, reducing cost and design time

Verilog, SystemVerilog and VDHL are all popular hardware description languages, in this course we mainly use VHDL, although it will be briefly compared to Verilog later on. 

![[Pasted image 20240228082848.png | center | 500]]

## A general model of a digital system

![[Pasted image 20240228082948.png | center | 400]]
- Digital circuits can be modelled based on their structure (how the components are connected and what is the behaviour of each component)
- Digital circuits can be modelled based on their behaviour (We can assume it consists of one large component and describe its behaviour)

### HDL Modelling of digital circuits
Digital systems consist of components that operate concurrently. Therefore and HDL description of digital circuits must be able to describe:
- The components that make up the system
- Their interconnection
- The behaviour of each of the components in terms of their input and output signals and the relation between them
- The circuits attributes requited for modelling and simulation 
	- Events - A change in the value of a signal, ie from 0 to one
	- Propagation delay -The amount of time required to propagate the input change to the output due to the inertia of natural resistance in electronic circuits
**In summary**
Hardware description languages must provide constructs to provide these attributes for a specific design. 
- Simulators use such description to mimic the physical system for verification
- Synthesis compilers use such descriptions to synthesise hardware (Based on the specified design constraints)


### VHDL Description of a Digital system

![[Pasted image 20240228083722.png | center | 500]]

**How can we model changes happening due to event generation**
![[Pasted image 20240228084315.png | center  | 500]]

