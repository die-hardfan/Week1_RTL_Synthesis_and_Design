# Gate Level Simulation - Labs

General GLS flow assuming RTL simulation and Synthesis is done.
```bash
iverilog <path to primitves.v> <path to .lib> <netlist filename> <tb filename>
./a.out
gtkwave <vcd file name>
```

## Example 1: ternary_operator_mux.v

Design code:
```bash
module ternary_operator_mux (input i0 , input i1 , input sel , output y);
	assign y = sel?i1:i0;
	endmodule
```

Netlist
![](/DAY4/images/ternopmux_stat.png)

![](/DAY4/images/ternopmux_synth.png)

RTL Simulation
![](/DAY4/images/ternopmux_console.png)

![](/DAY4/images/ternopmux_sim.png)

GLS
![](/DAY4/images/ternopmux_gls_console.png)

![](/DAY4/images/ternopmux_gls.png)
As a proof of GLS, we can check under uut and the ports are named differently, the same as in the netlist, shown below:

![](/DAY4/images/ternopmux_net.png)

---

## Example 2: good_mux.v

Design code:
```bash
module ternary_operator_mux (input i0 , input i1 , input sel , output y);
	assign y = sel?i1:i0;
	endmodule
```

Netlist
![](/DAY4/images/goodmux_stat.png)

![](/DAY4/images/goodmux_synth.png)

RTL Simulation
![](/DAY4/images/goodmux_console.png)

![](/DAY4/images/goodmux_sim.png)

GLS
![](/DAY4/images/goodmux_gls_console.png)

![](/DAY4/images/goodmux_gls.png)
As a proof of GLS, we can check under uut and the ports are named differently, the same as in the netlist, shown below:

![](/DAY4/images/goodmux_net.png)

---

## Synthesis-Simulation mismatch due to incomplete sensitivity list: bad_mux.v

Design code:
```bash
module bad_mux (input i0 , input i1 , input sel , output reg y);
always @ (sel) #incomplete sensitivity list
begin
	if(sel)
		y <= i1;
	else 
		y <= i0;
end
endmodule
```

The simulator refers to the sensitivity list, and any changes in the inputs i0 and i1 are not propagated to the output. 
But the synthesis tool generates a mux, as expected, leading to a mismatch between them. 

Netlist
![](/DAY4/images/badmux_stat.png)

![](/DAY4/images/badmux_synth.png)

RTL Simulation
![](/DAY4/images/badmux_console.png)

![](/DAY4/images/badmux_sim.png)
Orange box: sel changes from 0 to 1, so 'if' block gets executed and output = i1 value at the instant = 1
Blue box: sel changes from 1 to 0, so 'else' block gets executed and output = i0 value at the instant = 1
Brown box: sel changes from 0 to 1, so 'if' block gets executed and output = i1 value at the instant = 0
When there is no change (or activity) in sel, always block is not executed, hence y remains constant.

GLS
![](/DAY4/images/badmux_gls_console.png)

![](/DAY4/images/badmux_gls.png)
Despite the bad mux code, the synthesis tool interprets it as a multiplexer block.

![](/DAY4/images/badmux_net.png)

---

## Synthesis-Simulation mismatch blocking caveat: bad_mux.v

Design code:
```bash
module blocking_caveat (input a , input b , input  c, output reg d); 
reg x;
always @ (*)
begin
	d = x & c;
	x = a | b;
end
endmodule
```

Netlist
![](/DAY4/images/block_stat.png)

![](/DAY4/images/block_synth.png)
o21a1 -> A1|A2 & B --> a|b&c = d

RTL Simulation
![](/DAY4/images/block_console.png)

![](/DAY4/images/block_sim.png)
Due to blocking statements within always block (which equates sequential execution of statements), the previous value of x is considered, since x is not updated yet. Thus x acts like a flip flop considering the previous values and not the current ones.
Orange box: currently, {a, b, c} = 3'b001 which expects d = 0, but d = 1 which is the result of {a, b, c} = 3'b111 (where a, b are previous values as shown by the box)
Brown box: Similarly here, currently {a, b, c} = 3'd101 which expects d = 1, but d = 0 due to {a, b, c} = 3'b001 (where a, b are previous values as shown by the box)

GLS
![](/DAY4/images/block_gls_console.png)

![](/DAY4/images/block_gls.png)
Despite the erroneous code, the synthesis tool interprets it as a|b&c logic.

![](/DAY4/images/block_net.png)

---
