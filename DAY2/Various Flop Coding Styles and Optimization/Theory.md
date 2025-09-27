# Flip Flops and Types

## **What?**
Storage elements that store 1 bit of data.

## **Why?**
Transitions in a combinational circuit are not instantaneous, due to the delays of the gates that make it up. Hence, the outputs of combinational circuits are prone to glitches (momentary wrong value due to a change in the inputs, which happens because these changes take time to propagate through the gates to its output). More gates means more delays, so more glitches.
Modern-day chips have so many functions, each of which is ultimately a combinational circuit. If combinational circuits are cascaded, then the output will never be stable because of all the glitches. To avoid this, flip flops are used. FF shields its output from glitches in its input, so they don’t propagate from one combinational circuit to next. 
But also, having only combinational circuits means a large amount of time to evaluate the output (sum of propagation delays of gates in the path to output). To reduce the time taken, increase the frequency of operation, and thus increase the performance, FFs are used. This technique is commonly known as pipelining.
Typically, when a circuit is powered on, the state of FFs is unknown (or garbage). To prevent this stage from affecting the output, FFs are initialised to bit 0 or 1 using reset or set.

For example, consider the below circuit and corresponding waveform:

//glitch

### 1. Circuit Description
   
The design consists of two flip-flops (storage elements) that capture values on a clock edge.
```text
Y = A AND (NOT B)
```
### 2. Zero-Delay simulation Waveform (Ideal Case)

This represents the ideal scenario where all signals change instantly (no delays in gates or wires).

At the rising clock edge:

- `A` changes immediately.
- `B` changes immediately.
- `Y` updates instantly based on `A` and `B`.

### 3. Delay-aware simulation Waveform (Real Hardware)

In real hardware, signals don’t change instantly. Each gate and wire introduces a small delay.

After the clock edge:

- `A` changes at **6.3 ns**
- `B` changes at **6.34 ns** (due to buffer delay)

Because of this misalignment:

- `Y` first goes high at **6.44 ns**, but then quickly goes low again at **6.47 ns**. (which includes AND gate delay) 
- This short, unwanted pulse is called a **glitch**.
  
---

## **Types of FFs:**
Asynchronous set/reset FF
Synchronous set/reset FF
Asynchronous and synchronous reset FF
(set and reset – if applied together, causes a race condition, so it's not discussed here)

Asynchronous: set/reset works as soon as it is applied, irrespective of the clock signal.
Synchronous: set/reset signal waits for a clock edge, for it to be applied. 

---
## Labs
Simulate and synthesize different DFFs. 

**Note:** Asynchronous set/reset, only when asserted, is effective immediately. When the signal is de-asserted, the function of DFF  returns to normal only on the next clock edge, not immediately.

---

# Interesting optimizations in Hardware
1.	Multiply by 2
2.	Multiply by 8

Multiplication by 2 is essentially a left shift by 1 position. This can be done by rewiring the inputs to outputs, hence no standard cell is required. So, the ‘abc’ command throws an error, since there is nothing to map to. 
This is applicable for multiplication (and division) by any non-negative power of 2. 


