# Overview
## **Aim:** 
1.	Gate level simulation
2.	Simulation-synthesis mismatch
3.	Related labs

## GATE LEVEL SIMULATION
RTL code is simulated, using a TB, to check whether it is functionally correct. 
Similarly, to check if the synthesized circuit is functionally correct, GLS is done using the same TB used for RTL code simulation.
Since netlist is derived from the RTL code, they must be logically equivalent, then why is GLS required?
1.	Possible simulation-synthesis mismatch due to various reasons
2.	RTL code simulation is not timing aware. For a timing aware simulation (since in real world, it needs to be considered), GLS can be used. 
To perform delay annotated GLS, we need to have Verilog models (of standard cells) that have delay information along with the functional definition. Without the it, a functional validation of the synthesized circuit can be performed.
**Important note:** Gate level Verilog model is required to give the definition of the standard cells that are referenced in the netlist to the simulator.

GLS flow using iverilog:

![](/DAY4/images/glsflow.png)

## REASONS FOR SIMULATION-SYNTHESIS MISMATCH
1.	Missing sensitivity list
Sensitivity list is a reference for the simulator to call a block of code. It doesn’t affect the synthesis tool. So often, synthesis gives the correct output while simulator gives the wrong output.

2.	Careless usage of blocking assignments
Blocking statements denote a sequential execution, which is most times wrong when using for sequential circuits. It doesn’t simulate the parallelism/concurrency that is observed in hardware, hence it can lead to mismatches.

3.	Non standard HDL coding

Relevant commands for GLS:
```
iverilog <path to primitives.v> <path to sky130_fd_sc_hd.v> <netlist name> <testbench file name>
```
further steps remain same as simulation

## RELEVANT LABS
All the files are available in the repo.



