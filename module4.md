 MODULE 4:GLS, Blocking vs Non-Blocking and Synthesis–Simulation Mismatch

Objective:
The objective of this module is to understand gate-level simulation, the behaviour of blocking and non-blocking assignments, and the difference that can occur between RTL simulation and synthesized circuit simulation.

Theory:
Gate-level simulation is performed after synthesizing the RTL design into a gate-level netlist. It helps verify whether the synthesized hardware produces the expected output. Blocking and non-blocking assignments can affect the behaviour of a design depending on how they are used. Improper coding styles may cause a difference between RTL simulation and the synthesized design, which is known as synthesis–simulation mismatch.
Ternary Operator MUX
The ternary operator can be used to describe a multiplexer in Verilog. The select signal determines which input is transferred to the output. The design is simulated first to verify its functionality and then synthesized to observe how it is implemented using standard cells.
After synthesis, the design statistics show that the circuit is implemented using a multiplexer cell.
## 1. Overview & Objectives
The primary objective of Module 4 is to perform *Gate-Level Simulation (GLS)* on synthesized netlists, understand standard cell mapping, and analyze functional discrepancies that occur between behavioral RTL simulation and synthesized hardware logic.

Key objectives covered:
- Validating functional correctness using post-synthesis gate-level netlists.
- Investigating synthesis-simulation mismatches caused by improper Verilog coding styles.
- Analyzing the behavioral impact of *blocking (=)* versus *non-blocking (<=)* assignments inside procedural blocks.
<img width="1280" height="800" alt="gtkwave mux" src="https://github.com/user-attachments/assets/416d1c45-bef3-4cdd-b732-e01cce58ec6a" />
<img width="1280" height="800" alt="syn mul code1" src="https://github.com/user-attachments/assets/46d1ef13-9b2b-43fd-9cef-5fa8c0fc0b90" />

---

## Procedure

* Navigated to the verilog_files directory and launched Yosys synthesis suite in the terminal.
* Read the SkyWater 130nm PDK target technology library (sky130_fd_sc_hd__tt_025C_1v80.lib) and loaded the Verilog RTL design file (blocking_caveat_final.v).
* Elaborated the top-level module and executed synthesis targeting standard cell gates using synth -top blocking_caveat and abc -liberty.
* Exported the clean post-synthesis gate-level netlist file (blocking_caveat_final_netlist.v) without extra attribute metadata.
* Compiled the netlist, testbench (tb_blocking_caveat.v), and PDK behavioral model files (primitives.v and sky130_fd_sc_hd.v) using iverilog.
* Executed the compiled executable ./tb_blocking_caveat.ipp to generate the output .vcd file.
* Loaded tb_blocking_caveat.vcd into GTKWave to analyze and compare signal waveforms.

## 2. Theoretical Background

### Gate-Level Simulation (GLS)…
 Gate-Level Simulation (GLS) Execution
The synthesized netlist (blocking_caveat_final_netlist.v) was compiled alongside Sky130 PDK primitive behavioral models (primitives.v and sky130_fd_sc_hd.v).
 4. Conclusion & Key Takeaways
GLS Validation: Successfully verified the gate-level netlist against testbench vectors using iVerilog and GTKWave.
Coding Guidelines:
Always use non-blocking assignments (<=) for sequential logic (edge-triggered always blocks).
Always use blocking assignments (=) or continuous assign statements for purely combinational logic.
Maintain correct operational ordering inside procedural blocks when using blocking statements to prevent synthesis-simulation mismatches.

Bad MUX:
This section demonstrates the behaviour of an improperly described multiplexer. The RTL simulation is performed and the output waveform is observed. This helps in understanding how an incorrect coding style can affect the intended functionality of the circuit.


Blocking Assignment Caveat
Blocking assignments execute statements sequentially, and the order of execution can influence the values observed during simulation. When blocking assignments are used inappropriately, the RTL simulation result may differ from the actual hardware generated during synthesis.
The waveform is observed to understand how changes in the input signals affect the output signals.
<img width="1280" height="800" alt="blocing caveat diagram" src="https://github.com/user-attachments/assets/2ec75cfc-1ea4-4c1b-8455-b7381cfcbbda" />
<img width="1280" height="800" alt="tb blocking 2" src="https://github.com/user-attachments/assets/c723f9e7-e958-41f3-9063-236d7f3301b0" />


Synthesis–Simulation Mismatch:
RTL simulation verifies the original Verilog description, whereas gate-level simulation verifies the synthesized netlist. Ideally, both simulations should produce the same functional output. However, certain Verilog coding practices can result in different behaviour after synthesis.
This comparison helps identify a synthesis–simulation mismatch and shows the importance of using proper coding styles while writing RTL designs.
<img width="1280" height="800" alt="blocking vcd waveform" src="https://github.com/user-attachments/assets/9f896ccf-ec3e-48b2-bd93-3217cf065efd" />

## Result

The Module 4 experiments on Gate-Level Simulation (GLS) and synthesis-simulation mismatches were completed successfully. 
The gate-level simulation verified that out-of-order blocking assignments inside procedural blocks cause the RTL simulation model to evaluate inputs with stale values, whereas synthesized hardware operates as pure combinational logic.
Comparing the pre-synthesis RTL simulation against post-synthesis GLS in GTKWave conclusively proved the existence of synthesis-simulation mismatch caused by improper coding styles.
