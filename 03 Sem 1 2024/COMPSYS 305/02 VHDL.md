### Signals
Digitial circuits are about the operations on signals and signal transporting 
- To capture the behaviour of digital signals, the VHDL language introduces a new object type, the signal. 
- Signals can be used to connect components (like wires)
- Signals can be assigned values (like variables), but they receive values after some specific time
- The sequence of values assigned to a signal over time is called the waveform or the transaction of the signal

A signal represents a **Time-value** pair however, a variable only represents a value. 

![[Pasted image 20240228084641.png | center | 500]]

#### Signal assignment statements
Multiple signal assignment statements are executed concurrently in simulated time and are referred to as *Concurrent signal assignment statements*

![[Pasted image 20240228084757.png | center | 500]]
If an event (signal transaction) occurs on a signal that is on the right hand side of the signal assignment statement, the expression is evaluated and new values for the output signal are scheduled for some time in the future as defined by the `after` keyword.

`after` only has an effect if the signal is stable for >= the amount of time specified in the statement.?

#### VHDL Data types
![[Pasted image 20240228084953.png | center | 500]]
![[Pasted image 20240228085157.png | center | 500]]
`X"5A"` means hexadecimal, if no prefix provided then binary is assumed. Oct is ___

#### VDHL Operators (VDHL 93)
![[Pasted image 20240228085516.png | center | 500]]
Concatenation is combining two bit vectors to make one, longer bit vector. 

#### Examples of concurrent assignment
![[Pasted image 20240228090834.png | center | 500]]
```vhdl
architecture behaviour of hand_adder1 is 
begin
	sum <= (a xor b) after 5ns:
	carry <= (a and b) after 5ns:
end architecture behaviour;
```
![[Pasted image 20240228091004.png | center | 400]]
```vhdl
entity Ex2 is 
	port(a, b, c, d: in bit: z: out bit);
end entity Ex2

architecture arc2 of Ex2 is
	signal w, x, y: bit;
begin
	w <= a and b;
	x <= w or c;
	y <= (w or c or d);
	z <= x xnor y;
end architecture arc2;
```

Also valid, but not optimal: 
```vhdl
z <= ((a and b) or c) xnor ((a and b) or c or d)
```
#### Conditional signal assignment
In some cases it might be necessary to assign a new value to a signal under certain conditions. **Conditional signal assignments** can be used for these cases. 

![[Pasted image 20240228092014.png | center | 500]]

>Conditions are evaluated in the order they appear

##### Example
Modelling a 4 input multiplexer using conditional signal assignment (assume `2ns` propagation delay)
![[Pasted image 20240228092134.png | center | 300]]

```vhdl
entity Mux4 is 
	port(I0, I1, I2, I3: in bit; 
		S0, S1 in bit; 
		Z: out bit);
end entity Mux4
architecture mux of Mux4 is 
begin
	z <= I3 after 2ns when S0 = '1' and S1 = '1' else
		 I2 after 2ns when S0 = '1' and S1 = '0' else
		 I1 after 2ns when S0 = '0' and S1 = '1' else
		 I0 after 2ns when S0 = '0' and S1 = '0';
end architecture mux;

	
```

Whenever an event occurs on any one of the input signals (`I0` to `I3`) or any of the control signals (`S0` and `S1`) the express on the right hand side (RHS) of the conditional assignment is evaluated in the order they appear. 

> Therefore the order of the conditional expressions is important

#### Selected signal assignment
This is very similar to conditional signal assignments. The value of a signal is determined by the value of a select expression. 

Whenever an event on a signal in the select expression or on any signals used in any of the expressions, the statement is evaluated. 

![[Pasted image 20240228093112.png | center | 500]]

![[Pasted image 20240228093154.png | center | 400]]

```vhdl
...
signal x1: bit_vector(1 downto 0);
signal x2: integer;
...
with x1 select
	x2 <= 10 after 5ns when "00",
	x2 <= 20 after 12ns when "01",
	x2 <= 25 after 5ns when "10",
	x2 <= 32 after 15ns when others;
```

This behaves similarly to a switch case statement in higher level languages.  There is a `others`  keyword that behaves like `default`.  Use to state the value over of the target signal over unspecified ranged. 

In CSA (Conditional Signal assignment), the choices are evaluated in the order they appear, and the first true one is executed while the rest are ignored

In the SSA (Select signal assignment), all choices are evaluated but only one must be TRUE. Furthermore all of the choices must cover all of the possible values of the select expression. 

#### Bit Selects, Part selects, concatenation
When we are working with vector variables/signals (`bit_vector`), we may need to be able to 
- Select one bit or a group of bits (bit-select)
- Place different group of bits or parts into a new group (part-select)
- combine parts of vectors into a new vector (concatenation)

```vhdl
architecture a1 of ex1 is 
	signal A, B, C: bit_vector(3 downto 0);
begin
	...
	C(0) <= A(2) and B(0);
	C(1) <= A(0) and B(3);
	C(3 downto 2) <= A(2 downto 1) and B(1 downto 0);
	...
```
This ands parts of `A` and `B` to make Parts of `C`

##### Concatenation (&)
![[Pasted image 20240301091502.png | center | 300]]



#### Multiple Transactions

>[!info] Drivers
>The bit after the `<=` is the "driver", you can only have one driver assigning each signal

With respect to the current simulation time, the time-value pair for ht esignal represetns the future value to be assigned to the signal, and is referred to as a transaction

It is possible to specify multiple waveform elements resulting in multiple transactions to the signal assignment statment

```vhdl
S1 <= (a xor b) after 2ns, (a or b) after 8ns, (not b) after 13ns
```
> This statement will be evaluated only once as there are no signals that it depends on.. 

When an event occurs on either of the signals `a` or `b` then the above statement would be execute, all three waveform elements would be evaluated and three transactions would be generated. The list of transactions is referred to as the *driver* of the signal.

##### Waveforms
Waveforms can be created using multiple transaction statements

Write the VHDL code to generate the follwing waveforms: For `A`, `B` and `Z`. Note that `Z` is `A` and `B` when the input `X` is `1` 
![[Pasted image 20240301092356.png | center | 300]]

```VHDL
entity thing is 
	port(X: in bit, A, B, Z: out bit);
end entity thing
architecture arc2 of thing is
	signal e, f: bit;
begin
	e <= "0", "1" after 5ns;
	f <= "1", "0" after 5ns, "1" after 10ns, "0" after 15ns;
	
	A <= e;
	B <= f;
	Z <= X and e and f;
end architecture arc2
```

>[!tip] Output ports cannot be read!!


### Signal Attributes

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


![[Pasted image 20240313091229.png | center | 500]]