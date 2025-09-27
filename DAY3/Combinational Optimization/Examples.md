# Combination Optimization - Examples

## Example 1: opt_check.v

Design code
```bash
module opt_check (input a , input b , output y);
	assign y = a?b:0;
endmodule
```

Expected: 
```text
y = a'&0 | a&b
  = 0 | a&b
y = a&b
```

After synthesis: 
//optcheck
//optcheck_synth

---

## Example 2: opt_check2.v

Design code
```bash
module opt_check2 (input a , input b , output y);
	assign y = a?1:b;
endmodule
```

Expected: 
```text
y = a'&b | a&1
  = a'&b | a
  = (a' | a)&(b|a)
  = 1 & (a|b)
y = a | b
```

After synthesis: 
//optcheck2_stat
//optcheck2_synth

---

## Example 3: opt_check3.v

Design code
```bash
module opt_check3 (input a , input b, input c , output y);
	assign y = a?(c?b:0):0;
endmodule
```

Expected: 
```text
y = a'&0 | a&(c'&0 | c&b)
  = 0 | a&(0|b&c)
  = a&(b&c)
y = a&b&c
```

After synthesis: 
//optcheck3_stat
//optcheck3_synth

---

## Example 4: opt_check4.v

Design code
```bash
module opt_check4 (input a , input b , input c , output y);
 assign y = a?(b?(a & c ):c):(!c);
 endmodule
```

Expected: 
```text
y = a'&c' | a&(b'&c | b&a&c)
  = a'&c' | a&b'&c | a&b&c
  = a'&c' | a&c&(b'|b)
  = a'&c' | a&c&1
y = a'c' + ac = (a^c)' or a xnor c
```

After synthesis: 
//optcheck4_stat
//optcheck4_synth

---

## Example 5: multiple_module_opt.v

Design code
```bash
module sub_module1(input a , input b , output y);
 assign y = a & b;
endmodule


module sub_module2(input a , input b , output y);
 assign y = a^b;
endmodule


module multiple_module_opt(input a , input b , input c , input d , output y);
wire n1,n2,n3;

sub_module1 U1 (.a(a) , .b(1'b1) , .y(n1));   // n1 = a & 1
sub_module2 U2 (.a(n1), .b(1'b0) , .y(n2));  // n2 = n1 ^ 0
sub_module2 U3 (.a(b), .b(d) , .y(n3));      // n3 = b ^ d

assign y = c | (b & n1);


endmodule
```

Expected: 
```text
y = c | (b&a&1)
  = c | b&a
y = a&b|c
```

After synthesis: 

Method 1:
```bash
read_liberty <.lib path>
read_verilog multiple_module_opt.v
synth -top multiple_module_opt
abc -liberty <.lib path>
flatten
show
opt_clean -purge  #removes wires and internal signals not connected to output, makes circuit look clean
show
```
Results:

![](/DAY3/images/multmodopt_stat.png)

![](/DAY3/images/multmodopt_synth.png)

![](/DAY3/images/multmodopt_synth_purged.png)
As seen, this flow doesn't give the most optimized output. It gives: y = (a&1)&b|c

Method 2:
```bash
read_liberty <.lib path>
read_verilog multiple_module_opt.v
synth -flatten
abc -liberty <.lib path>
show
opt_clean -purge  #removes wires and internal signals not connected to output, makes circuit look clean
show
```
Results:

![](/DAY3/images/multmodopt_synth2.png)

![](/DAY3/images/multmodopt_synth2_purged.png)
Clearly, flattening the circuit before mapping to the standard cells results in a more optimized circuit.

---

## Example 6: multiple_module_opt2.v

Design code
```bash

module sub_module(input a , input b , output y);
 assign y = a & b;
endmodule



module multiple_module_opt2(input a , input b , input c , input d , output y);
wire n1,n2,n3;

sub_module U1 (.a(a) , .b(1'b0) , .y(n1));  #n1 = a & 0
sub_module U2 (.a(b), .b(c) , .y(n2)); #n2 = b&c
sub_module U3 (.a(n2), .b(d) , .y(n3)); #n3 = n2 & d
sub_module U4 (.a(n3), .b(n1) , .y(y)); #y = n1&n3


endmodule
```

Expected: 
```text
y = (a&0) & ((b&c)&d)
y = 0
```

After synthesis: 
![](/DAY3/images/multmodopt2_stat.png)
Clearly, there is nothing to map and it shows in the statistics.

![](/DAY3/images/multmodopt2_synth.png)

---


