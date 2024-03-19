In VHDL'93 there are four classes of objects. 
- Signals
- Variables
- Constants
- Files
```vhdl
signal signal_name: signal_type [<= initial_value];
variable variable_name: variable_type [:= initial_value];
constant constant_name: constant_type [:= initial_value];

```
These are the built in data types. 
![[Pasted image 20240313082822.png | center | 400]]

#### Enumerated Type
```vhdl
type week_w_days is ('mon', 'tue', 'wed', 'thu', 'fri'); 
variable today : week_w_days := 'tue';
```

#### Array Types
```vhdl
type byte is array (7 downto 0) of bit;
type word is array (15 downto 0) of bit;
type mem is array (0 to 1023) of word;

signal PC: word := x"FFF0" --hex
variable instr_mem : mem;
```
#### Record Type
```vhdl
type instruction_type is record
	opcode : bit_vector(5 downto 0);
	s_reg1, s_reg2, d_reg : integer range 0...31;
end record;
```

or alternatively

```vhdl
subtype reg_num is integer range 0...31;
subtype opcode_t is bit_vector (5 downto 9);
type instruction_type is record
	opcode: opcode_t;
	s_reg1, s_reg2, d_reg : reg_num;
end record;
```


#### 1KB Memory
```vhdl
entity Mem1 is 
	port(clk, rd, wr: in bit;
	address: in integer
	data_in: in bit_vector(7 downto 0);
	data_out: out bit_vector(7 downto 0))
end entity Mem1;
architecture behaviour of mem1 is
	type MeM_array is array (0 to 1023) of bit_vector(7 downto 0);
	signal mem: mem_array;
begin
	process (clk)
	begin
		if(clk`event and clk='1' ) then
			if(rd = '0' and wr = '1') then
				mem(address) <= data_in;
			elsif(rd='1' and wr = '0')
				data_out <= mem(address);
			end if
		end if
	end process
end architecture behaviour
```