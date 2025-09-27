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
