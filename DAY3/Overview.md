## DAY 3
**Aim:** 
1.	Combination optimization techniques
2.	Sequential optimization techniques
3.	Unused logic optimization 
4.	Related labs

## LOGIC OPTIMIZATION
**Why?** 
To get the best circuit in terms of area, power or delay.
**Types:** Combinational and sequential optimization

## COMBINATIONAL OPTIMIZATION TECHNIQUES
1.	Constant propagation
When any of the inputs receive a constant value (1 or 0), this constant propagates down to the output, thus simplifying the whole circuit. 
2.	Boolean optimization
Simplifying the expressions using Boolean laws, K-maps, Quine McClusky method etc. 

Most of the time, such optimizations are automatically applied by the synthesis tool. Sometimes, extra steps are involved. 
For example, to make multimodule designs most optimized, it must be flattened. The above techniques are then applied to the flattened design to give the most optimal circuit. 

## SEQUENTIAL OPTIMIZATION TECHNIQUES
1.	Sequential constant propagation (basic)
In special cases, when the output of a DFF is constant (independent of set, reset or any other signal), DFF can be removed and treated as a constant.
2.	State optimization (advanced)
The number of states of FSMs can be reduced by removed unused/redundant states. This decreases the number of DFFs required.
3.	Sequential logic cloning (advanced)
This is floor plan aware synthesis. If two or more DFFs are far away physically, it can create huge routing delays which could cause timing violations. So DFFs with large positive slack are cloned to reduced the distance between other DFFs. 
4.	Retiming (advanced)
To increase the operating frequency, based on the possibility, the combinational logic in pipeline stages can be redistributed. Basically, more logic is added to the stage with more positive slack, from a stage with less positive slack. The functionality must remain same throughout. 

## UNUSED LOGIC OPTIMIZATION
If any of the primary outputs of a module is completely independent of some logic that is defined in the RTL code, it is removed by the synthesis tool, as an optimization. 

## RELEVANT LABS
All the files are linked in the repo.
