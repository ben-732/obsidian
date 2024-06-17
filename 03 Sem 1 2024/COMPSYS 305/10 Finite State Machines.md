Digital circuits can be categorised into two distinct types
**Combinational** A circuit whose output is a function of its present inputs, it has no memory
**Sequential** A circuit whose output depends on both the current inputs and its current state. 
- The current state is the combination of all bits currently stored
- The circuits state in turn depends on the past sequence of input values.


A sequential circuit is essentially formed by a collection of combinational logic and memory elements (registers).

![[Pasted image 20240424081638.png | center | 300]]

Here the combinational logic determines the next state in the next clock cycle. 

In most digital system designs, we partition the system into two types of modules:
**Data path:** preforms data-processing operations
- Acts as an execution unit. 
- All necessary resources and interconnections among the resources are provided to perform a specified task. 
- Resources include adders, multipliers, logic gates, registers, etc.
**Control Unit:** Determines the sequence of those operations
- Controls data movements in an operational circuit by switching multiplexers and enabling or disabling resources. 
- Follows some program or schedule.
- Often implemented as Finite State Machine (FSM) or collection of Finite State Machines.

![[Pasted image 20240424081844.png | Center | 400]]
Example signals:
- Control Input: `start`, `stop`, `pause`, `add`
- Data Input: `A`, `B`
- Data Outputs: `C`, `S`
- Status Signals: `Done`, `Zero`

## State Machines
A circuit is often in the form of a verbal description of the behaviour. It needs an interpretation to find a state diagram or state table.

A state is used to remember something about the history of the inputs. The values stored in the state register will represent the current state of the circuit.  The next state and outputs are both a function of the current state and inputs.

![[Pasted image 20240424082817.png | center | 300]]
### Machine Model
- States: $S_{1}, S_{2}, \dots , S_{k}$
- Inputs $I_{1}, I_{2}, \dots, I_{m}$
- Outputs: $O_{1}, O_{2}, \dots, O_{n}$
- Time: Discrete time steps
- Transition function: $F_{s}(S_{k},\, I_{m})$
- Output Function: $F_{o}(S_{k})$ or $F_{o}(S_{k},\, I_{m})$ - Depending on type of machine

A state machine with a finite number of states is called a **finite state machine**.

The state diagram represents the FSM in a graphical way:
- Circles represent different states
- Arcs represent the transition from one state to another, The number on an arc is important
- Outputs can be in a circle or on an arc separated by a slash `/`

The state table shows state diagram information in a tabular form. State diagram can be easily translated into stable table during analysis and design process.

With n-bit state register, FSM can represent a maximum of $2^n$ states.


### Design Models
FSMs can be designed using two different models:

**Moore Model**
A Moore machine's outputs depends on only the current state
**Mealy Model**
A mealy machine is designed such that the outputs depend on both the state and current inputs


![[Pasted image 20240424083740.png | center | 400]]

When drawing a state diagram, the outputs are represented differently.

In a Moore FSM state diagram, Inputs are shown on the arc and outputs are shown in the circle.
![[Pasted image 20240424084032.png | center | 300]]

In a Mealy FSM state diagram, Inputs **and** outputs are both shown on the arc
![[Pasted image 20240424084135.png | center | 300]]

FSM to detect the sequence of "10" on a one-bit input.

Mealy
![[Pasted image 20240424085519.png | center | 500]]

Moore
![[Pasted image 20240424091219.png |center | 500]]

Moore and mealy FSMs can be functionally Equivalent. Mealy FSM can be derived from Moore FSM and vice versa. 

Mealy FSM has richer description and usually requires smaller number of states.
- Smaller circuit area.  
Mealy FSM computes outputs as soon as inputs change.
-  Mealy FSM responds one clock cycle sooner than equivalent Moore FSM.
-  Mealy machines response not only to the state but also the inputs within the same clock cycle rather than wait for the next one.

Moore FSM has no combinational path between inputs and outputs.
- Moore FSM is more likely to have a shorter critical path.

### FSMs in VHDL
Finite State Machines can be easily described with processes. Synthesis tools understand FSM description if certain rules are followed.
- State transitions should be described in a process sensitive to clock.
- Asynchronous reset signal could be also in the sensitivity list.
- Outputs can be de described as concurrent statements outside the process.

#### Designing a parity checker
The aim is designing an FSM which outputs the parity on a sequence of input. We start with state diagram of a Mealy machine.

![[Pasted image 20240424092317.png | center | 400]]