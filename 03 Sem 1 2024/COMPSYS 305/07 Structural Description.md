Structural modelling consists of: 
- Interconnecting design objects (entities, which are used as components) to create a structure having a desired functionality. 
- Each component by itself can be a behavioural model or structural model (of some other components).

![[Pasted image 20240315091503.png | center | 500]]
![[Pasted image 20240315091518.png | center | 400]]
### Using Components
Assume that we have two components: Half-Adder and 2-input OR gate. We want to build a Full-Adder circuit using these components.

![[Pasted image 20240315091602.png | center | 400]]
```vhdl
entity Half_Adder is 
	port(x, y: in std_logic; sum, carry: out std_logic);
end entity Half_Adder
```

When it is used in a structural declaration as a  component
```vhdl
component Half_Adder is 
	port(x, y: in std_logic; sum, carry: out std_logic);
end component Half_Adder
```

Using components follows the syntax
```
Label: Component-Name port map()
```
#### Port Mapping

Use `port map()` to state how the inputs and output ports of a component are connected to other signals in the circuit. 
```
port map (signals of the component => signals of the circuit )
```


There are two methods of port mapping
**Named association:**
```vhdl
H1: Half_Adder port map(x =>In1, y=> in2)
```

**Positional Association**
```vhdl
H1: Half_Adder port map(In1, In2);
```

Name association is preferred as it is clearer. 

#### Full Adder Example


![[Pasted image 20240315091933.png | center | 400]]
```vhdl
entity Full_Adder is 
	port (in1, in2, C_In: in std_logic; Sum, C_out: out std_logic)
end entity Full_Adder
```
Components: `Half_Adder`, `OR_2`
Connecting Signals: `s1`, `s2`, `s3`

```vhdl
architecture Structural of Full_Adder is 
	component Half_Adder is 
		port (x, y : in std_logic; sum, carry : out std_logic); 
	end component Half_Adder; 
	
	component OR_2 is 
		port (x, y : in std_logic; z : out std_logic); 
	end component OR_2;

	signal s1, s2, s3 : std_logic;
begin
	H1: Half_Adder 
		port map (x => In1, y => In2, sum => s1, carry => s3); 
	
	H2: Half_Adder 
		port map (x => s1, y => C_In, sum => sum, carry => s2); 
	
	O1: OR_2 
		port map (x => s2, y => s3, z => C_out);
end architecture structual
```




#### Hierarchy Models
Structural models simply describe interconnections. They do not describe any form of behaviour.

>[!example]
>In the `Full_Adder` example, we have two components (`Half-Adder`, and `OR_2`). Each of these components in turn might be described as a structural model of other components or as a behavioural model. 
>
>Now, we assume that `Half_Adder` itself is described as a structural model.
>![[Pasted image 20240315092926.png | center | 400]]

Structure in half adder
![[Pasted image 20240315092952.png | center | 300]]

### Generics
The use of generics enables us to write generic models whose behaviour in a particular simulation is determined by the value of the generic parameters.

Generics appear very much like ports. They are a part of the interface specification of the component. However, unlike ports, they do not have a physical interpretation. Generics are constant objects which convey information through the design hierarchy. 

>We use generics to describe parameterised models

In a structural model, there are two ways to specify generics
1. In the component declaration
2. In the component instantiation statement using the `generic map( )` construct.

If both are specified, the value provided by the `generic map( )` takes precedence. If neither are specified, then the default value defined in the model is used.

Generic constants are passed through multiple levels of the hierarchy.

When you want to assign all bits of a vector, you can do `v_q := (others => '0')`


#### Example
Creating an *n-input parameterised OR gate*

If we are going to use this Generic_OR in a design with a different value of N (say N = 8), generic map(N => 8) is used to define the new size

```vhdl
library IEEE; 
use IEEE.std_logic_1164.all; 
entity Generic_OR is 
	generic (N: positive := 2); 
	port (In1: in std_logic_vector((N -1) downto 0); 
		Z: out std_logic); 
end entity Generic_OR;
architecture behaviour of Generic_OR is
begin
	process (In1) is 
		variable x: std_logic := ‘0’; 
	begin 
		x := ‘0’; --on an input signal transition ,x must be initialized to 0 
		for I in 0 to (N-1) loop 
			x := x or In1(I); 
		end loop; 
		
		Z <= x; 
	end process;
end architecture behaviour;
```