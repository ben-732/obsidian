### Packages

Packages are a group of logically related sets of type definitions, functions, and procedures which can be shared across many VHDL models (code). 

Packages and subprograms provide an opportunity for modular programming in VHDL. 

In packages, the external view of the items (item declaration) is separated from their implementation. The external view is provided through package declaration and the implementation is defined by the package body.

Package decleration:
```vhdl
package package_name is 
	[ package-declarative-items ] 
end [package] [package_name]
```

Package body
```vhdl
package body package_name is 
	[ package-body-declarative-items ]
end [package body] [package_name] ;
```

#### Example package
```vhdl
package my_CPU_types is 
	constant word_size : positive := 16; 
	constant address_size : positive := 24; 
	subtype word is bit_vector(word_size - 1 downto 0); 
	subtype address is bit_vector(address_size - 1 downto 0); 
	type status_value is (halted, idle, fetch, mem_read, mem_write, io_read, io_write,            int_ack); 
	subtype opcode is bit_vector(5 downto 0); 
	function Extract_Opcode( instr_word: word) return opcode; 
	constant op_nop: opcode “000000”; 
	constant op_add: opcode “000100”; 
	constant op_lda: opcode “000101”; 
	constant op_sta: opcode “000110”; 
	constant op_jsr: opcode “000111”; 
end package my_CPU_types;
```
```vhdl
package body my_CPU_types is 
	function Extract_Opcode( instr_word: word) 
		return opcode is variable result: opcode; 
	begin 
		case instr_word(15 downto 10) is 
			when “000000” => opcode := op_nop; 
			when “000100” => opcode := op_add; 
			when “000101” => opcode := op_lda; 
			when “000110” => opcode := op_sta; 
			when “000111” => opcode :=op_jsr; 
		end case; 
		
		return result; 
	end function Extract_Opcode; 
end package body my_CPU_types
```
### Libraries

The `library` keyword declares the design library to be used. 

The `use` keyword imports the package and the specified declarations from within the package (types, subprograms, constants etc)

`library library_name`; 
`use library_name.package_name.package_item[s]`

`use IEEE.std_logic_1164.all;` - all means all items of the package. 

VHDL provides five types of design units: `entity`, `architecture`, `package`, `package body`, and `configuration`. 

The VHDL compiler (analyser) compiles each design unit and places it in a specified design library. The default library (which is considered as the current design library) is library `work`.

> A `work` library must be specified before compiling VHDL files

As it is the default library, it is not needed to use library clause to declare it.

- work library is implicitly visible. 
- Other libraries must be declared in the beginning of a design file using library clause. 
- Specific Design Units can be specified in a design library using a use clause. 
- Library `STD` is loaded automatically. (It provides `TEXTIO` package, etc.)

![[Pasted image 20240313092414.png | center | 400]]

#### IEEE VHDL Library
##### std_logic
IEEE Provides a standard library to extend data types and some functions in VHDL. It is IEEE standard 1664.1993

Normally the type `bit` only has two values. `1` or `0`. In IEEE 1164 it has 9 values. 
With IEEE 1664 we use `std_logic` instead of `bit` and `std_logic_vector` instead of `bit_vector`

| value | interpretation  |
| ----- | --------------- |
| U     | Uninitialized   |
| X     | Forcing Unknown |
| 0     | Forcing 0       |
| 1     | Forcing 1       |
| Z     | High Impedance  |
| W     | Weak Unknown    |
| L     | Weak 0          |
| H     | Weak 1          |
| -     | Dont Care       |

##### Overloads and helpers
The library also overloads some operators to improve functionality. 

`+` and `-` are overloaded to worth with `std_logic` and `std_logic_vector`

Functions `rising_edge(signal_name)` and `falling_edge(signal_name)` return `TRUE` when a rising-edge or falling edge activity occurs on their signals. 

In order to use these features, the IEEE library and package `IEEE.std_logic_1164.all` must be declared in design file. For arithmetic operations on `std_logic` and `std_logic_vector` types for example, package `IEEE.std_logic_unsigned.all` must be declared in design file.


##### Other packages
Two packages are provided in IEEE standard for integer arithmetic operations: 

`numeric_bit` This is used for arithmetic operations on variables and signals of bit type library IEEE; 

`use IEEE.numeric_bit.all;` 
type unsigned is array (natural < >) of bit 
type signed is array (natural < >) of bit 

`numeric_std` This is used for arithmetic operations on variables and signals of `std_logic` type library IEEE; 

`use IEEE.numeric_std.all;` 
type unsigned is array (natural < >) of std_logic 
type signed is array (natural < >) of std_logic 

Signed numbers are represented in two’s complement form.

![[Pasted image 20240313093712.png | center | 500]]

For `std_logic_unsigned`
![[Pasted image 20240313093737.png | center | 500]]


```vhdl
signal tmp : bit_vector(7 downto 0); 
begin 
	tmp <= “10011111” when digit = “0001” else 
		“00100101” when digit = “0010” else 
		“00001101” when digit = “0011” else 
		“10011001” when digit = “0100” else 
		“01001001” when digit = “0101” else 
		“01000001” when digit = “0110” else 
		“00011111” when digit = “0111” else 
		“00000001” when digit = “1000” else 
		“00001001” when digit = “1001” else 
		“00000011” when digit = “0000” else 
		“11111111”; -- 1 -- 2 --3 --4 --5 --6 --7 -- 8 -- 9 -- 0 
		
	LED_out <= tmp when (all_off = ‘0’) else “11111111”;
```

