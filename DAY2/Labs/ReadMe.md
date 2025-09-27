# Heirarchical vs Flat Synthesis - Labs

Consider the following code in ```multiple_module.v``` : 

```bash
module sub_module2 (input a, input b, output y);
	assign y = a | b;
endmodule

module sub_module1 (input a, input b, output y);
	assign y = a&b;
endmodule


module multiple_modules (input a, input b, input c , output y);
	wire net1;
	sub_module1 u1(.a(a),.b(b),.y(net1));  #net1 = a&b
	sub_module2 u2(.a(net1),.b(c),.y(y));  #y = net1|c ,ie y = a&b + c;
endmodule
```
### 1. Sub-modules

**sub_module1**  
- Takes two inputs: `a` and `b`.  
- Outputs `y` which is the **AND** of `a` and `b`.  
- Think of it as an **AND gate**.  

**sub_module2**  
- Takes two inputs: `a` and `b`.  
- Outputs `y` which is the **OR** of `a` and `b`.  
- Think of it as an **OR gate**.  


### 2. Top module: multiple_modules

- **Inputs:** `a`, `b`, `c`  
- **Output:** `y`  

**Inside this module:**  
- A wire called `net1` holds intermediate results.  
- Instances of `sub_module1` and `sub_module2` are created.  
- Final output:  
---

## Synthesize this using Yosys.

```bash
read_liberty -lib /root/week1_files/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog multiple_modules.v
synth -top multiple_modules
abc -liberty /root/week1_files/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
write_verilog -noattr multiple_module_heir.v
!gvim multiple_module_heir.v #'!' used to access terminal from within yosys
show multiple_modules # show <module_name> is the command for showing a particular module when multiple are present
```

//multmodsynth

From the statistics, we see that hierarchy is preserved in the multiple_modules module. This can be seen in the schematic and the netlist as shown below:

//heir mult mod
Here we can see that submodules are synthesized as AND and OR gate respectively.

//mult mod synth

To flatten the circuit (remove the heirarchy), use the following code within yosys environment:
```bash
flatten
write_verilog multiple_modules_flat.v
show     #no need of module name since there is only 1 overall module in a flattened circuit
!gvim multiple_module_heir.v -O multiple_module_flat.v #to compare the netlists
```
From below images, we see that submodules are replaced by their synthesized counterparts.

//multmod flat
//compare heir flat

## Sub-module level synthesis 
Given a large design, we can synthesize each sub-module at a time, using the command:

```bash
synth -top <sub_module_name>
```
Basically, this command synthesizes only the module mentioned as top. The following images show this in practice:

//submod_synth

The statistics show the inference of 1 AND gate only, which proves the point.

//submod_synth_stat

---

# Various Flip Flop coding styles - Lab

## Simulation 

We're looking into 4 varieties of FFs - Async reset, sync reset, async set and async reset with sync reset. The code for each of them is given below:

//various dff design

Async reset/set: The reset/set signal is included in the sensitivity list, so that the always block of code is triggered when they are asserted, as show in the waveform below.

//ares_sim

//ares_1
When async_res is asserted, irrespective of the clk edge, q becomes 0.

//ares_2
But when async_res is deasserted, q waits till the next clk edge to follow d.

This is because in the design code, the sensitivity list includes 'posedge' of async_res, so only the assertion is truly asynchronous in nature. The always block is not sensitive to the de-assertion event of async_res since it is not included in the sensitivity list, hence, it follows the clk edge.

Similarly, for the Asynchronous set, we see the following images:

//sres_sim

//sres

//sres_2

Sync reset: The reset signal is not included in the sensitivity list, hence it gets triggered only at the clk edge after it has been asserted. The de-assertion is also synchronous in nature.

//syncresetsim

//sres_1

But, for an FF with both async_res and sync_res, it is important to note that because of the code (where async_res is checked before sync_res), async_res is given priority over sync_res. So, when async_res is high, irrespective of sync_res, q is 0, as show below:

//ares_sres_sim

//ares_sres_1

---

## Synthesis

General flow for synthesizing any circuit with FF in it:
Note: Here, .lib file for logic gates and FFs is the same.

```bash
read_libery <.lib path>
read_verilog <verilog file>
dfflibmap -liberty <path of .lib that contains FFs>  
abc -liberty <.lib path>
write_verilog -noattr <netlist name>
show
```

Async reset DFF: 

//ares_s1

//ares_stat

//ares_synth

Sync reset DFF: 

//sres_stat

//sres_synth

Async set DFF: 

//aset_stat

//aset_synth

Async res Sync res DFF: 

//ares_sres_stat

//ares_sres_synth

---

# Interesting Optimizations in Hardware

## Multiplication by 2

Design code: 

```bash
module mul2 (input [2:0] a, output [3:0] y);
	assign y = a * 2;
endmodule
```

Synthesizing it, we get:

//mult2_stat

From the above image we see that, when 'abc' command is executed, no cell is inferred/mapped because it's not required.

//mult2_synth

---

## Multiplication by 9

Design code:

```bash
module mult8 (input [2:0] a , output [5:0] y);
	assign y = a * 9;
endmodule
```

Synthesizing it, we get:

// mult8_stat

//mult8_synth
