## Aim
Familiarisation with the tool flow and commands required for the workshop.

---

## Tools used

- Iverilog (simulator)  
- GTKwave (waveform viewer)  
- Yosys (synthesizer)  

---

## Theory

### Introduction

Any design flow starts with specifications (in short *spec*) which is given by the customer. It entails the detailed functionality of what the design/module is supposed to do.

An implementation of spec is called **RTL design**. 

To check/verify whether the implementation (RTL design) adheres to given specifications, it needs to be simulated, which is done using a simulator (e.g. iVerilog).

Simulation is done by applying test vectors/stimulus to the primary inputs of the DUT (design under test) and observing its primary outputs. This requires a stimulus generator and observer, all of which together forms the **testbench**. 

A testbench has no primary inputs and outputs, since all it does is apply stimulus and observe the outputs of the DUT. 
![](/DAY1/images/tb_setup.png)

---

### How a Simulator Works

Below image describes the simulation flow in iverilog.

![](/DAY1/images/iverilog_sim_flow.png)

A simulator looks for the changes in inputs; when there is a change in any of the inputs, the outputs are evaluated.

**No change in input → no change in output**, since it's not evaluated by the tool. 

The output file of a simulator is called a **Value Change Dump (.vcd)**, which refers to the fact that the tool evaluates and "dumps" the output to a file when there’s an input **value change**.

---

### Lab: Simulation of ```good_mux.v``` file

The code describes the behaviour of a 2:1 multiplexor:

```
module good_mux (input i0 , input i1 , input sel , output reg y);
always @ (*)
begin
	if(sel)
		y <= i1;
	else 
		y <= i0;
end
endmodule
```
- Module definition: The block is named good_mux. It has three inputs: the first input, the second input, and a select signal. It has one output.

- Select signal decides: the select signal acts like a controller.
  - If the select signal is high (one), the output will follow the second input.
  - If the select signal is low (zero), the output will follow the first input.

- Always block: The always block is written so that it continuously checks the inputs and select signal. Whenever any of them change, the output is updated.

- Output type: The output is declared as "register" type, but in this context it just means it is assigned inside the always block. It does not store values like memory here.

- Behavior: the circuit takes two possible signals, and the select signal decides which one appears at the output.
