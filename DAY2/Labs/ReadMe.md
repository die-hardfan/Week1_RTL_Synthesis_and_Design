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
![](/DAY2/images/mult_mod_synth.png)

From the statistics, we see that hierarchy is preserved in the multiple_modules module. This can be seen in the schematic and the netlist as shown below:

![](/DAY2/images/multmod_synth.png)

![](/DAY2/images/heir_mult_mod_netlist.png)

Here we can see that submodules are synthesized as AND and OR gate respectively.
![](/DAY2/images/heir_mult_mod.png)


To flatten the circuit (remove the heirarchy), use the following code within yosys environment:
```bash
flatten
write_verilog multiple_modules_flat.v
show     #no need of module name since there is only 1 overall module in a flattened circuit
!gvim multiple_module_heir.v -O multiple_module_flat.v #to compare the netlists
```
From below images, we see that submodules are replaced by their synthesized counterparts.

![](/DAY2/images/multmod_flat.png)

![](/DAY2/images/compare_heir_flat.png)

## Sub-module level synthesis 
Given a large design, we can synthesize each sub-module at a time, using the command:

```bash
synth -top <sub_module_name>
```
Basically, this command synthesizes only the module mentioned as top. The following images show this in practice:

![](/DAY2/images/submod_synth.png)

The statistics show the inference of 1 AND gate only, which proves the point.

![](/DAY2/images/submod_synth_stat.png)

---

# Various Flip Flop coding styles - Lab

## Simulation 

We're looking into 4 varieties of FFs - Async reset, sync reset, async set and async reset with sync reset. The code for each of them is given below:

![](/DAY2/images/various_dff_design.png)

Async reset/set: The reset/set signal is included in the sensitivity list, so that the always block of code is triggered when they are asserted, as show in the waveform below.

![](/DAY2/images/ares_sim.png)

![](/DAY2/images/ares_1.png)
When async_res is asserted, irrespective of the clk edge, q becomes 0.

![](/DAY2/images/ares_2.png)
But when async_res is deasserted, q waits till the next clk edge to follow d.

This is because in the design code, the sensitivity list includes 'posedge' of async_res, so only the assertion is truly asynchronous in nature. The always block is not sensitive to the de-assertion event of async_res since it is not included in the sensitivity list, hence, it follows the clk edge.

Similarly, for the Asynchronous set, we see the following images:

![](/DAY2/images/sres_sim.png)

![](/DAY2/images/sres.png)

![](/DAY2/images/sres_2.png)

Sync reset: The reset signal is not included in the sensitivity list, hence it gets triggered only at the clk edge after it has been asserted. The de-assertion is also synchronous in nature.

![](/DAY2/images/sync_reset_sim.png)

![](/DAY2/images/sres_1.png)

But, for an FF with both async_res and sync_res, it is important to note that because of the code (where async_res is checked before sync_res), async_res is given priority over sync_res. So, when async_res is high, irrespective of sync_res, q is 0, as show below:

![](/DAY2/images/ares_sres_sim.png)

![](/DAY2/images/ares_sres_1.png)

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

![](/DAY2/images/ares_s1.png)

![](/DAY2/images/ares_stat.png)

![](/DAY2/images/ares_synth.png)
The DFF uses is asynchronous active low reset, but our code suggests active high reset, hence an inverter is used.

Sync reset DFF: 

![](/DAY2/images/sres_stat.png)

![](/DAY2/images/sres_synth.png)
Here, a nor gate is used. It can be proved as: 
```text
D = (sync_res | d')'
	= sync_res' & d
if sync_res = 0,
D = 1 & d = d
```
This means, D = d when sync_res is 0, which follows the behaviour of our code.

Async set DFF: 

![](/DAY2/images/aset_stat.png)

![](/DAY2/images/aset_synth.png)
The DFF uses is asynchronous active low set, but our code suggests active high set, hence an inverter is used.

Async res Sync res DFF: 

![](/DAY2/images/ares_sres_stat.png)

![](/DAY2/images/ares_sres_synth.png)
This seems like a combination of Sync reset and Async reset DFF circuits.

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

![](/DAY2/images/mult2_stat.png)

From the above image we see that, when 'abc' command is executed, no cell is inferred/mapped because it's not required.

![](/DAY2/images/mult2_synth.png)
This means, {a[2:0], 0} --> y[3:0] (left shift by 1 basically)

---

## Multiplication by 9

Design code:

```bash
module mult8 (input [2:0] a , output [5:0] y);
	assign y = a * 9;
endmodule
```

Synthesizing it, we get:

![](/DAY2/images/mult8_stat.png)

![](/DAY2/images/mult8_synth.png)
This is because a*9 = a*(8+1) = a<<3 + a and if a is 3 bit number, then,
 {a[2:0], a[2:0]} --> y[5:0]
