![[Pasted image 20240306083900.png | center | 500]]

To describe the internal behaviour of components of a digital system in VDHL, a construct called `process` is used. 

Behaviour of the circuit is described by using sequential statements, via the `process` construct. `process` constructs are used to model algorithms and complex components such as CPU, memory modules, communication protocols, etc

Generally, process constructs are used when:
- The complete behavioural description of the circuit required high-level programming constructs (sequential statements)
- We need to retain state information within the component description over time
- Computation of the time at which output events are generated based on input changes is complex. 
## Process Statements
In contrast to signal assignment statements, a process describes a sequentially executed block of code. 

![[Pasted image 20240306084238.png | center | 300]]
- Items in brackets are optional
- Any number of sequential statements may be in a process

#### How it works
If the process has a **Sensitivity list**
- When an event occurs on any signals in the sensitivity list, the process is executed once and then it is suspended. 
- New events may be generated and scheduled during the process execution. 

If the process does not have a sensitivity list
- There must be a special statement called the *wait statement* in the process. The process is executed and then suspended when the wait statement is executed. Some mechanisms are provided through the wait statement in order to make the process to be re-executed after suspension. 
- New events may be generated and scheduled during the process execution. 

#### Variables 
Variables are used to maintain values used during the simulation within a process. They behave similar to static vars in C

```vhdl
variable_name := expression;
```
Variables are only declared/defined in a process statement and their scope is the process in which they are declared. 

#### Signals
Signals are assigned values using a signal assignment statement. Signals must be declared out of a process (in the declaration part of an architecture).

A signal assignment statement within a process is considered to be a sequential signal assignment statement and is executed in sequence with respect to the other sequential statements which appear within that process.

#### Differences
The expression of the variable assignment statement is evaluated at the current simulation time and the value is assigned to the variable at the evaluation time of the variable assignment statement. 

The expression of the signal assignment statement is also evaluated at the current simulation time. However the value is assigned to the signal after delay-value (delta delay) and not at the evaluation time. 

Signals in a process are used to communicate to the outside of the process including other processes. But variables can only be used within the process that are declared. (There are some exceptions like for shared variables in VHDL 1993/2000)

Objects that represet signals in the modelled system should be represented as signals in your VHDL code. Objects that are used simply to compute values of signals scan use variables. 

Remember the processes are used toe describe a sequence of computations to determine the values that are to be assignned to signals. Their assignemnts do not take place until the process has finished executing (or suspended). On the other hand, variables act just as we expect form a conventional high-level language. 
##### Example
```Vhdl
architecture b1 of example is
begin
	process (A, B, C, D)
		variable Temp1, Temp2: bit;
	begin
		Temp1 := A and B;
		Temp2 := C and D;
		Temp1 := Temp1 or Temp2;
		Z <= not Temp1;
	end process;
end architecture B1;
```

The sensitivity list is the `(A, B, C, D)` bit after `process`


### Execution Order
All concurrent statements are executed in parallel. 
In the beginning of the simulation, all process statements and concurrent signal assignments are evaluated, the the corresponding transactions are scheduled to occur in the future. They are then suspended until an event triggers the start of their execution. 

Once a process is activated, all its statements (called sequential statements) are evaluated until it reaches the end of a process (or wait statement). 

Signal statements inside a process are called *sequential signal assignments* their right hand side is evaluated at the current simulation time, but the new value is assigned after the delta delay. Signals must be declared outside the process. 

Variable assignment statements are evaluated at the current time and their new value is assigned at the current time as well. Variables must be declared inside a process statement. 


### Conditional statements
##### If/else
```vhdl
if boolean-expression then
	sequential-statements
elsif boolean then 
	sequential-statments 
else
	sequential-statments 
end if;
```

`else` and `elsif` statements are optional 

##### Case statements
```vhdl
case expression is
	when choices => sequential-statments;
	when choices => sequential-statments;
	when others -> sequential-statments;
end case;
```

the `expression` must be a discrete type or of a 1D array. 

All possible values of `expression` must be covered in the case statements, the `others` keyword is available to provide default behaviour. 

##### Ways to model MUX4
![[Pasted image 20240306092557.png | center | 500]]

### Signal Events

*Attributes* in VDHL are used to query about an object (such as a signal) to determine some types of information about the object. For example, `event` is an attribute for signals which returns `TRUE` if an event occurs on the signal, otherwise `FALSE`.

If you ad a signal called `clk` you could get the event attribute like this: `clk'event`

```vhdl
entity counter is 
	port(reset, clk: in bit: q; out integer);
end entity counter

architecture arc2 of counter is
begin
	process (clk)
		variable count_q: integer;
	begin
		if(clk`event and clk = '1') then
			if(reset = '1') then
				count_q := '0';
			else then
				count_q := (count_q + 1) mod 5;
			end if;
		end if;
		Q <= count_q;
	end process
end architecture arc2;
```
>[!note]
>\`  used instead of `'` becuase of syntax highlighting

### Loops
There are three types of iteration-schemes provided. The general form is:
```vhdl
[loop-label: ] iteration-scheme loop
	sequential-statements
end loop [loop-lable];
```

#### For loop
`for identifier in range`
```vhdl
for n in 1 to 10 loop
	X := X + 2;
end loop;
```

#### While loop
`while boolean-expression`
```vhdl
while A < 10 loop
	Z := Z * 2;
	A := A + 1;
end loop;
```

#### No iteration scheme loop
The third type does not have an iteration scheme. 
```vhdl
L1: loop
	Y:= Y+1;
	exit when Y > 20;
end loop L1;
```

#### Loop Statements
Some specific sequential programming statements can be used only in loops
- `exit`  like break, causes the execution to jump out of the innermost loop or the loop who's lable is specified. when no loop label is specified, the innermost loop is exited. 
	- `exit [label] [when condition];
- `next` like continue, It causes to skip to the next iteration. 
	- `next [label] [when condition];`

### Null
Null does nothing, is just placeholder ?
![[Pasted image 20240308093855.png | center | 300]]
### Wait statement
The `wait` statement provides a way to suspend or resume the execution of a process pending on the occurrence of events. 

The wait statement explicitly specifies the conditions under which a process may resume execution after being suspended. 

```vhdl
wait for time-expression;
wait until boolean-expression;
wait on signal;
wait;
```
These forms could also be combined into a single wait statement. If any one of the terms in the statement occurs / becomes true, then the process will resume. 

```vhdl
wait for 5ns;
wait until x = y;
wait on Clk, Reset;
wait on A,B until C = D, for 20ns;
wait on click until x > 10;
wait until y < 100 for 100ps;
wait on clk for 100ns;
```

>[!important] Note
>A process can only have either `wait` statement(s) or a sensitivity list

#### 0ns wait
`wait 0 ns` causes the process to suspend for one delta cycle. This means that signal assignments do thigns


## Subprograms
VHDL Provides subprograms which consist of sequential programming statements to perform a certain task.

Subprograms may be declared and defined in either:
- An architecture  (Visible to all processes in the architecture)
- A process (Visible only to the process its defined in)

There are two types of subprograms
- Functions - A subprogram that executes in zero simulation time and returns a single value. It cannot contain wait statements
- Procedures - A subprogram that can modify the input parameters. It may have wait statements and therefore may execute in non-zero simulation time. 

Procedures are usually used to partition large behavioural descriptions

### Function
```vhdl
function funciton_name(input_parameters_sepcification)
	return value_type is
	item-declerations
begin
	subprogram-statements
end function funciton_name;
```

input parameters indicate the name and type of formal parameters of the function. Each formal parameter may be an object type `signal`, `variable`, `file` or `constant`. Formal parameters may not be modified inside the function. 

The only mode of these parameters is `in` which does not need to be mentioned explicitly. `Variables` and `constant` objects are no required to be mentioned explicitly. 


```vhdl
function Find_Maximum (variable A : in int_array) 
	return integer is variable Max_Value: integer; 
begin -- assume the first element is the maximum and correct it later 
	if not Max_Value := A(0); 
		for J in 1 to 9 loop 
			if A(J) > Max_Value then 
				Max_Value := A(J); 
			end if; 
		end loop; 
	return Max_Value; 
end function Find_Maximum
```

`end _function_` and `: _in_ int_array` underscore bits are optional. 

### Procedures
```vhdl
procedure procedure_name (input_output_parameters_specification) is
	item-declarations
begin
	subprogram-statements
end [procedure] [procedure_name];
```

`input_output_parameters_specification` indicates the name and type of formal input and output parameters of the procedure. Each formal parameter may be an object of type `signal`, `variable`, `file`, or `constant`. 

Formal parameters declared as out or inout mode can be modified inside the procedure. `Variables` and `constant` objects are not required to be mentioned explicitly.

