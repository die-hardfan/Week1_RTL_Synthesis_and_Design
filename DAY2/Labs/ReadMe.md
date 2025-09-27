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

