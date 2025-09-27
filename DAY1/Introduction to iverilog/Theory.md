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
//add image of tb here

---

### How a Simulator Works

A simulator looks for the changes in inputs; when there is a change in any of the inputs, the outputs are evaluated.

**No change in input → no change in output**, since it's not evaluated by the tool. 

The output file of a simulator is called a **Value Change Dump (.vcd)**, which refers to the fact that the tool evaluates and "dumps" the output to a file when there’s an input **value change**.
