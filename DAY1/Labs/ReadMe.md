# Introduction to iVerilog - Labs

All the relevant tools have been installed as part of Week0 tasks. All the relevant files are available on a github account and needs to be cloned, as given below:

1. Open the terminal, check if git is installed using the command:

    ```bash
    git --version
    ```
2. Get the url for the github repository.
   
3. Then use the `git clone` command on the terminal in the desired folder:

    ```bash
    git clone <repo url> <folder name to store the files>
    ```
//add image of git clone

---

**The directories within the repo are as follows:**

- `.lib` : is the sky130 standard cell library to be used for synthesis  
- `mylib` : contains the Verilog models of the standard cells in `.lib`  
- `verilog_files` : contains all the design files and testbench used in the labs  

---
## Steps for simulation in iVerilog

- Navigate to `verilog_files` folder

- Simulate:

    ```bash
    iverilog <design file names> <tb file names>
    ```

    This creates `a.out` file. Execute it using:

    ```bash
    ./a.out
    ```

    This produces a `.vcd` file. To view it:

    ```bash
    gtkwave <vcd file name>
    ```
//add img of terminal
//add image of waveform - goodmux

---

# Introduction to Yosys - Labs


**Aim:** Synthesis of `good_mux.v`

1. Invoke yosys
    ```
    yosys
    ```
2. Read the `.lib` file:

    ```bash
    read_liberty -lib <path to .lib>
    ```

   This imports the standard cells from the library file.

3. Read design file:

    ```bash
    read_verilog <design_file_name>.v
    ```

4. Synthesis:

    ```bash
    synth -top <module name of the design to be synthesized>
    ```

5. If the design contains DFFs, then:

    ```bash
    dfflibmap -liberty <dfflib file path>
    ```

   This is done to map the DFFs to the standard cells that are sometimes stored in a different file.

6. Then generate the netlist:

    ```bash
    abc -liberty <path to .lib file>
    ```

   This generates the netlist by mapping the standard cells from `.lib` file to the synthesized circuit above.

7. Write the netlist:

    ```bash
    write_verilog -noattr <netlist file name>.v
    ```
    
   (Usually netlist file name is <module_name>_net.v)
   Using the gvim text editor, we can view the netlist as a text.  
   `-noattr` is used for a simplified view of the netlist.
   
9. To view the synthesized circuit
    ```bash
    show
    ```

//invoke_yosys

//synth1

//synth stat
True to the design code, the synthesized circuit has the same number of primary inputs and outputs. Hence, the same testbench used for RTL simulation can be used to verify the netlist as well.

//synth2

**After mapping to the standard cells**

//goodmux_synth
