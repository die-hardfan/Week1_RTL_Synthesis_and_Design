# Introduction to Logic Synthesis

RTL design _describes_ how the required spec behaves. But our goal is to get a circuit in the end. To convert the code (representation) to hardware, synthesis is done.
In simple words, synthesis translates RTL code to gate-level representation using standard cells given in .lib file.

![](/DAY1/images/synthflow.png)


.lib file is one of the inputs to the synthesis process. It's basically a collection of logical modules with different flavours for each of them (slow, typical/medium, fast). It might not be an exhaustive list (that contains all possible logic modules), but its rich enough to implement any logic that can be described.
A NAND/NOR gate, called universal gate, is sufficient to convert any logic described into circuit, but for better optimization and efficiency (and to makes life easier), .lib contains a wide variety of standard cells. The richer the library, more options for optimization available.

---
## Why different flavours of gates?

Consider the below circuit: 

![](/DAY1/images/dff_comb.png)

The input data (D) is transferred to output (Out) at clock edge. So total delay taken for this to occur depends on 
- time taken for change in clk signal to reflect at Q1 (t_clk_q1)
- delay of the combinational logic (t_comb)
- time before the next clk edge by which D2 must be stable for FF2 to properly capture D2 to Q2 (t_setup2)
Assuming the output must be given once every clk edge and considering the worst-case delays, we can say:
1 period of clk (Tclk) >= t_clk_q1 + t_comb + t_setup2 (setup time constraint)
Max operating frequency of the circuit is inverse of Tclk, which implies to maximize circuit frequency (thus improving the performance) Tclk must be minimized. But t_clk_q and t_setup depend on the FF itself and not in our hands. Hence, we can only minimize t_comb as much as possible.
Then why not use only fast cells, why are slow or typical cells required at all? In short, there is a hold time constraint.
The idea of adding FFs to the input and output of a purely combinational circuit is to avoid glitches and get a better hold of input and output data (sequencing the inputs and outputs). Accordingly, if FF1 launches the D in the current clk cycle, FF2 must capture D2 in the next clk cycle.
In short, we need to avoid FF1 and FF2 to launch and capture at the same clk cycle. Because otherwise, FF2 will lose the prev cycle data that needs to be captured in the current clk cycle. Thus, after the clk edge, input to FF1 and FF2 must remain stable for some amount of time, called the hold time.
t_hold < t_comb + t_clk_q1 (hold time constraint)
According to this, we need to increase t_comb, where slow cells can be used. But this clearly contradicts the setup time constraint.

From this we conclude that only slow cells or only fast cells can not satisfy necessary constraints, hence a mix of both is essential. Thus different flavours of cells are available in the library.

---
## Fast vs Slow cells

Simply, fast cells have less cell delay and slow cells more. Typical cells have an intermediate delay value.
Delay (or cell delay) means the amount of time taken for the change in input to reflect in the output of the cell. The time taken to charge or discharge the parasitic capacitance in the cell causes the delay.
So the faster the input charges the capacitance, the less the delay. The rate of charging by the input of a cell is driven by the output of the previous cell. To charge or discharge the capacitance fast, we need transistors capable of sourcing more current, which requires wide transistors since I_D is directly proportional to W (for MOSFETs).
In short, wide transistors means less delay but this consumes more area (cuz more W) and power (cuz more I). 
This proposes a tradeoff between speed of the cell and area, power consumed. 

---
## Selection of cells

To reach the desired spec (in terms of performance(time), power, area), _the designers_ need to guide the synthesis tool to select cells of appropriate flavour for optimal implementation of design.
Usage of more fast cells means:
- increased consumption of area and power
- possible hold time violations
But, usage of more slow cells means:
- sluggish circuit
- required performance not met

Hence, the guidance provided to the tool is very important, and this guidance is called **constraints**.

## Synthesis Flow (RTL to Netlist)

### 1. Read RTL code
- The tool takes your Verilog/VHDL design as input.  
- It checks the syntax and makes sure the design is correct.  

### 2. Elaboration
- The tool expands your code, resolves parameters, loops, and generates the complete circuit structure as understood by the tool.  

### 3. RTL analysis
- The tool looks at your code and builds a high-level logic representation  
  (for example: "this is a multiplexer", "this is an adder").  

### 4. Optimization
- The tool simplifies the logic (removes unnecessary gates, merges operations).  
- It also applies design constraints (like timing requirements, area, or power goals).  

### 5. Mapping to standard cells
- The tool maps your logic to real hardware cells from a library  
  (for example: 2-input AND gate, D flip-flop, multiplexer cell).  
- This step makes the design **technology-specific**.  

### 6. Netlist generation
- Finally, the tool writes a **netlist**.  
- A netlist is basically a big list of gates and wires that shows how everything is connected.  

![](/DAY1/images/synth_mapping.png)

---
## Labs

Synthesize the ```good_mux.v``` design using Yosys.
