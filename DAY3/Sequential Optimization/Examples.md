## VARIOUS INSTANCES OF SEQUENTIAL CONSTANT PROPAGATION

## EXAMPLE 1

Design code: 
```
always @(posedge clk, posedge reset)
begin
	if(reset)
		q <= 1'b0;
	else
		q <= 1'b1;
end
```

From simulation we can see that the output pin q is not always constant, hence this DFF must be retained, it can't act as a sequential constant.

![](DAY3/images/dffconst1_sim.png)
Simulation

![](DAY3/images/dffconst1_stat.png)

![](DAY3/images/dffconst1_synth.png)
Actual circuit


## EXAMPLE 2

Design code:
```
//code 2
always @(posedge clk, posedge reset)
begin
	if(reset)
		q <= 1'b1;
	else
		q <= 1'b1;
end
```
Simulation of code 2 - the output q doesn't change, remains constant, so this DFF can be removed and it can act as a sequential constant.
![](DAY3/images/dffconst2_sim.png)

![](DAY3/images/dffconst2_synth.png)
Actual circuit

![](DAY3/images/dffconst2_stat.png)
'abc' command error for code 2 shows that the circuit doesn't have any cells to map to.

## Example 3:

Design code: 
```bash
//code 3
always @(posedge clk, posedge reset)
begin
	if(reset)
	begin
		q <= 1'b1;
		q1 <= 1'b0;
	end
	else
	begin
		q1 <= 1'b1;
		q <= q1;
	end
end
```

Simulation of code 3 - the output q changes, so this DFF must be retained to maintain the functionality of the cirtuit.
![](DAY3/images/dffconst3_sim.png)

![](DAY3/images/dffconst3_stat.png)

![](DAY3/images/dffconst3_synth.png)
Actual circuit


## Example 4: 

Design code:
```
//code 4
always @(posedge clk, posedge reset)
begin
	if(reset)
	begin
		q <= 1'b1;
		q1 <= 1'b1;
	end
	else
	begin
		q1 <= 1'b1;
		q <= q1;
	end
end
```
Simulation of code 4 - the output q doesn't change, remains constant, so this DFF can be removed and it can act as a sequential constant.
![](DAY3/images/dffconst4_sim.png)

![](DAY3/images/dffconst4_stat.png)

![](DAY3/images/dffconst4_synth.png)
Actual circuit

## Example 5: 

Design code:
```
//code 5
always @(posedge clk, posedge reset)
begin
	if(reset)
	begin
		q <= 1'b0;
		q1 <= 1'b0;
	end
	else
	begin
		q1 <= 1'b1;
		q <= q1;
	end
end

```
Simulation of code 5 - the output q changes, so this DFF must be retained to maintain the functionality of the cirtuit.
![](DAY3/images/dffconst5_sim.png)

![](DAY3/images/dffconst5_stat.png)

![](DAY3/images/dffconst5_synth.png)
Actual circuit


