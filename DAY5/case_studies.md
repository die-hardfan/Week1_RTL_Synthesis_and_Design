# CASE STUDIES 

## Overlapping case : bad_case.v

Design code:
```bash
module bad_case (input i0 , input i1, input i2, input i3 , input [1:0] sel, output reg y);
always @(*)
begin
	case(sel)
		2'b00: y = i0;
		2'b01: y = i1;
		2'b10: y = i2;
		2'b1?: y = i3;
		//2'b11: y = i3;
	endcase
end

endmodule
```

Netlist
![](/DAY5/images/badcase_stat.png)


![](/DAY5/images/badcase_synth.png)


RTL Simulation
![](/DAY5/images/badcase_console.png)


![](/DAY5/images/badcase_sim.png)


GLS

![](/DAY5/images/badcase_gls.png)



---

## Complete if : comp_case.v

Design code:
```bash

module comp_case (input i0 , input i1 , input i2 , input [1:0] sel, output reg y);
always @ (*)
begin
	case(sel)
		2'b00 : y = i0;
		2'b01 : y = i1;
		default : y = i2;
	endcase
end
endmodule
```

Netlist

![](/DAY5/images/compcase_synth.png)


RTL Simulation
![](/DAY5/images/compcase_sim.png)



GLS

![](/DAY5/images/compcase_gls.png)


---
## Incomplete case consequences

Not listing all the possible cases of a case constant leads to inferred latch, with appropriate enable logic.
In the code below, 2'b10 and 2'b11 cases are not defined and there is not default statement, so the simulator and synthesis tool will infer a latch. In simulation it can be seen that when sel[1] is high, the output is a constant. And its seen in GLS too, so there's no synthesis-simulation mismatch. But due to bad coding, our design to code a combinational circuit has turned into a sequential circuit which is a blunder.


![](/DAY5/images/badcase_synth.png)


RTL Simulation

![](/DAY5/images/badcase_sim.png)


GLS

![](/DAY5/images/badcase_gls.png)

In the synthesized circuit, an active low enable Dlatch is used, hence the enable condition is sel[1], unlike in predicted diagram. Also, a 2:1 mux is used, with select line s as nand(sel1', sel0) = sel1 + sel0'. 
When sel1 is 1, (for sel = 10 and 11) output is latched as en = 0.
When sel1 is 0, for sel = 00, s = 1, output is i1 and for sel = 01, s = 0, output = i0.

---
## Incomplete if consequences

## CASE 1

In the below code, else condition is not given, thus creating an inferred latch. 
```
	if(i0)
		y <= i1;
```

As seen in the simulation, initially, as long as i0 = 0, y is 'x' or since its previous state is unknown. Similarly, when initialised, and i0 becomes 0, y takes the value of i1 just before i0 became 0. 

![](/DAY5/images/badcase_synth.png)


RTL Simulation

![](/DAY5/images/badcase_sim.png)


GLS

![](/DAY5/images/badcase_gls.png)


## CASE 2

In the below code, else condition is not given, thus creating an inferred latch. 
```
	if(i0)
		y <= i1;
	else if (i2)
		y <= i3;
```

As seen in the simulation, initially, as long as i0 = 0 and i2 = 0, y is 'x' or since its previous state is unknown. Similarly, when initialised, and i0, i2 both become 0, y takes its value just before i0 and i2 both become 0. 

![](/DAY5/images/badcase_synth.png)


RTL Simulation

![](/DAY5/images/badcase_sim.png)


GLS

![](/DAY5/images/badcase_gls.png)

---

## Partial case assignment consequences

If all the outputs are not assigned a value in every case, as in the code below, it leads to an inferred latch even when a default statement is properly defined. 

```
	case(sel)
		2'b00 : begin
			y = i0;
			x = i2;
			end
		2'b01 : y = i1;
		default : begin
		           x = i1;
			   y = i2;
			  end
	endcase

```

y is synthesized into a proper mux but x is not. Synthesis-simulation mismatch is present.
The expected and actual circuits are as given below.

![](/DAY5/images/badcase_synth.png)

---

## for-generate

### mux_generate.v 

```bash
module mux_generate (input i0 , input i1, input i2 , input i3 , input [1:0] sel  , output reg y);
wire [3:0] i_int;
assign i_int = {i3,i2,i1,i0};
integer k;
always @ (*)
begin
for(k = 0; k < 4; k=k+1) begin
	if(k == sel)
		y = i_int[k];
end
end
endmodule

```

### demux_generate.v

```bash

module demux_generate (output o0 , output o1, output o2 , output o3, output o4, output o5, output o6 , output o7 , input [2:0] sel  , input i);
reg [7:0]y_int;
assign {o7,o6,o5,o4,o3,o2,o1,o0} = y_int;
integer k;
always @ (*)
begin
y_int = 8'b0;
for(k = 0; k < 8; k++) begin
	if(k == sel)
		y_int[k] = i;
end
end
endmodule

```
### demux_case.v

```bash

module demux_case (output o0 , output o1, output o2 , output o3, output o4, output o5, output o6 , output o7 , input [2:0] sel  , input i);
reg [7:0]y_int;
assign {o7,o6,o5,o4,o3,o2,o1,o0} = y_int;
integer k;
always @ (*)
begin
y_int = 8'b0;
	case(sel)
		3'b000 : y_int[0] = i;
		3'b001 : y_int[1] = i;
		3'b010 : y_int[2] = i;
		3'b011 : y_int[3] = i;
		3'b100 : y_int[4] = i;
		3'b101 : y_int[5] = i;
		3'b110 : y_int[6] = i;
		3'b111 : y_int[7] = i;
	endcase

end
endmodule

```

### Ripple carry adder

```bash
module rca (input [7:0] num1 , input [7:0] num2 , output [8:0] sum);
wire [7:0] int_sum;
wire [7:0]int_co;

genvar i;
generate
	for (i = 1 ; i < 8; i=i+1) begin
		fa u_fa_1 (.a(num1[i]),.b(num2[i]),.c(int_co[i-1]),.co(int_co[i]),.sum(int_sum[i]));
	end

endgenerate
fa u_fa_0 (.a(num1[0]),.b(num2[0]),.c(1'b0),.co(int_co[0]),.sum(int_sum[0]));


assign sum[7:0] = int_sum;
assign sum[8] = int_co[7];
endmodule

#full adder
module fa (input a , input b , input c, output co , output sum);
	assign {co,sum}  = a + b + c ;
endmodule

```







