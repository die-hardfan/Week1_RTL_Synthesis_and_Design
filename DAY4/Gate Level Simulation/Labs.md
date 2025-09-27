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
