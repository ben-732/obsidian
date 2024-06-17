Synthesis is the process of constructing a gate level **net list** from a register transfer level (RTL) or behavioural description of the system based on the target technology. 

A List of gates and their connections is called net list.

![[Pasted image 20240320081146.png | center | 500]]

#### What EDAs do 
![[Pasted image 20240320081331.png | center | 500]]
They target a technology: Standard Cells, Gate arrays (such as PLD, FPGA, ASIC)

Main steps: Partition, map, place and route interconnect In the synthesis process: 

The VHDL description will be mapped to the available hardware primitives. 

Delay information associated with the signal assignment statement (with `after` clause) is ignored by the synthesis compiler.

### Synthesis
#### Data Types
The following data types are usually accepted by sybnthesis tools. Others are tool dependant

**Enumeration types**
This includes predefined types boolean, bit and character
- boolean and bit are implemented in hardware as individual bits
- character as group of bits
- user-defined enumeration types may be implemented by a tool- dependent encoding
**Integer types**
- implemented as group of bits (negative numbers usually in two’s complement form)

**One-dimensional arrays of scalar elements,** 
This includes the predefined `bit_vector` 
- implemented as group of bits 

**`std_logic`, `std_logic_vector`**
Defined in package `std_logic_1164` 
- implemented in hardware as bits or group of bits 

**Unsigned and signed** 
Defined in package `numeric_bit` (or package `numeric_std`) 
- implemented in hardware as bits or group of bits

>[!info] Implementation
> Signal definitions are implemented as wires, latches and flip-flops. 
> - In a combinational circuit, signal values are carried by wires. 
> - In a sequential circuit, signal values are stored in latches or flip-flops.

The synthesis compiler infers logical operations from each of the VHDL signal assignment statements and utilises the gate or other primitives to implement these operations. 

This step is referred to as operator inference. The interconnection between gates or other primitives is determined by the dependencies between the signal assignment statements.

#### Signals
Both selected [[02 VHDL#Conditional signal assignment|signal assignment statements]] and [[02 VHDL#Selected signal assignment| selected signal assignment]] statements are inferred as `multiplexer` by synthesis tool. 

However, in conditional signal assignment, the conditions are not required to be mutually exclusive, so the synthesis tool would infer a priority encoded chain of multiplexers to conform with the language semantics. 

This structure might be slower than a simple multiplexer. The tool may be able to optimise the hardware, but it is better to use a selected signal assignment to imply a multiplexer. 

#### Processes
Sequences of statements in a process lead to the synthesis of combinational logic when every output signal is assigned a value on any execution of the process.

For example, for an `if` statement this means that all output signals must be assigned a value with in the `then` and `else` parts. 

This is also the case for nested `if-else` statements. The `case` statement follows the same rule. The `case` statement is more efficient than nested `if-else` when there is no priority ordering among the alternatives.

Otherwise, latches or flip-flops are inferred and the synthesised circuit will be a sequential circuit. To infer a **combinational circuit** from a process, ==all inputs must be declared in the sensitivity list of the process==. Otherwise, latches may be inferred or the synthesised circuit behaviour may be different.

>[!example]-
>![[Pasted image 20240320083203.png | center | 300]]
>Input C is not in the sensitivity list. So it is not considered as a correctly specified combinational circuit. Combinational circuit is inferred. 

Among the loop statements usually the `for-loop` is supported for synthesis. (Some tools may support other loops in some special cases). 

At compile-time the amount of hardware to be synthesised must be inferred from the VHDL code. For example, for a while-loop the number of loop iterations may not be known in advance (so the synthesis tool cannot determine the amount of hardware required for the synthesised loop statement).

![[Pasted image 20240320083755.png |center| 500]]
##### Summary
Sequences of statements in a process lead to the synthesis of combinational logic when every output signal is assigned a value on any execution of the process. 

For example, for an if statement this means that all output signals must be assigned a value within the then and else parts. This is also the case for nested if- else statements. The case statement follows the same rule. • The case statement is more efficient than nested if-else when there is no priority ordering among the alternatives. Otherwise, latches or flip-flops are inferred and the synthesised circuit will be a sequential circuit. 

To infer a combinational circuit from a process, all inputs must be declared in the sensitivity list of the process. Otherwise, latches may be inferred or the synthesised circuit behaviour may be different. 

Usually only the for-loop is supported for synthesis. (Some tools may support other loops in some special cases, if the number of iterations can be known somehow at compile time).


##### Wait statements
Most synthesis tools only allow the following form of wait statement to be used in the VHDL code: `wait until condition;` 
for example: `wait until (clk’event and clk = ‘1’);` or `wait until (rising_edge(clk));`


Only one wait statement is permitted in a process and it must be the first statement in the process. Therefore, if we use an edge-detection expression in the wait statement a flip-flop will be inferred for every signal in the process. 

When we want to create latches or flip-flops for only some signals with larger blocks of combinational circuit it is better to use if-then statement with an edge detection expression. In this case, flip-flops are inferred for the signals assigned in the body of the if-then statement.

##### Latch vs Flip-flop Inference
Generally, `if`, `case`, `wait`, conditional signal assignment statements, and selected signal assignment statements can be used to infer latches because they describe programming constructs where the code is conditionally executed. 

If the conditional part is specified as level detection, latches may be inferred. 
```vhdl
if (x = ‘1’) then -- latch is inferred 􏰀
```

If the conditional expression represents edge detection, flip-flops may be inferred. 
```vhdl
if (rising_edge(clk) then -- flip-flop is inferred or 
if (clk’event and clk = ‘1’) then -- flip-flop is inferred
```

>[!example]
![[Pasted image 20240320085258.png | center | 200]]
> Adding an `else w <= 'X' -- "we don't care value"` means that a latch is not synthesised, and instead combinational circuit is produced?
> 

>[!example]
>![[Pasted image 20240320085618.png | center | 400]]
![[Pasted image 20240320085555.png | center | 400]]

### Programmable Logic Devices
The structure of a PDL
![[Pasted image 20240320092418.png | center | 500]]

Complex Programmable logic devices (CPLDs) can only be programmed once, if u have a new design you need to buy a new chip. They are cheap tho (i think?)

Alternatively you can get **Field Programmable Gate Arrays** FPGAs



![[Pasted image 20240320092939.png | center | 500]]
LUT = Lookup table

![[Pasted image 20240320093110.png | center| 500]]
![[Pasted image 20240320093143.png | center | 500]]
![[Pasted image 20240320093422.png | center | 500]]
Here `M` bit indicates programming of the specific resource on FPGA. 

The basic structure of a LE (Logic Element) may be extended in different ways for different FPGA devices, based on the target applications' characteristics. In addition, somme additional special-purpose blocks (or resources) may be added. 

A 1 bit adder/subtractor is added to the basic LUT (Lookup table) and FF (Flip Flop) as it is needed for many operations

![[Pasted image 20240320093630.png | center | 300]]
Becomes
![[Pasted image 20240320093645.png | center | 300]]

A Group of LEs (like 16) makes an LAB (Logic Array Block) to use faster routing channels. 
#### FPGA Examples

![[Pasted image 20240320093917.png | center | 600]]

An altera CYCLONE device:
![[Pasted image 20240320093950.png | center | 500]]

Part of an Altera Stratix device architecture
![[Pasted image 20240320094056.png | center | 600]]
This LE architecutre is very similar to the cyclone devices.

##### Altera Cyclone III 
Logic element:
![[Pasted image 20240320094211.png | center | 600]]

Device
![[Pasted image 20240320094255.png | center | 500]]

![[Pasted image 20240320094328.png | center | 600]]

##### Cyclone V

ALM High level block diagram:
![[Pasted image 20240320094437.png | center | 500]]

### Using Quartus II for simulation
> In this course, we are using Quartus 18.1

![[Pasted image 20240320094824.png | center |500]]
The result of the Filter is converted into a programming image (bit stream) to configure the target FPGA device. Different formats can be used at this step. 

**Analysis & Synthesis:** All design files are analyzed and integrated into a single database. Then the synthesis process is performed to extract and minimize the logic of the design. 
**Fitter:** In this step the logic of the design is mapped into a device. The Fitter selects appropriate interconnection paths, pin assignments, and logic cell assignments. 
**Timing Analysis:** In this step all delays for the synthesized design are computed based on the selected device. This information is annotated on the netlist for subsequent use by other tools (for example, a simulator). In addition, timing analysis is performed to find out if the timing constraints of the design (for example, the minimum specified clock frequency) are satisfied.
**Simulator:** Functional or timing simulation is performed to verify the correctness of design functionality after synthesis and fitting. Simulation can be used to debug the model of the design. 
**Assembler:** In this step, the results of the Fitter is converted into a programming image for the device to configure it. Depending on the needs different formats can be used at this step. 
**Netlist Writer:** This generates output netlist files for use with other EDA tools. (For example, the new file can be in VHDL as .vho file)