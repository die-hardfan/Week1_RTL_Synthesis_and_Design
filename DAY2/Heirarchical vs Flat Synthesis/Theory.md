## HIERARCHICAL VS FLAT SYNTHESIS

- **Hierarchical synthesis** retains the submodules used within the main module after synthesis.  
- **Flat synthesis** creates one module with logic of submodules combined.  

> Optimization of multimodule designs fare better when the design is flattened.

### Relevant commands:

```bash
synth -top <module_name>         # Hierarchical
synth -flatten                   # Flat (auto-selects top module)
```
All relevant files (inputs and outputs) are given above.

## SUBMODULE LEVEL SYNTHESIS

Command:
```bash
synth -top <sub_module_name>
```
**Why?**
1.	When a design has multiple instances of the same submodule, it can be synthesized once and the netlist replicated in the final design. This saves time, specially if the submodule is complex/large.
2.	In massive designs, divide and conquer approach is applied. A large design is subdivided into smaller ones, and each is synthesized individually (perhaps in parallel to save time). The tool gives the most optimized version for each submodule, hence the overall design is more optimized. 
