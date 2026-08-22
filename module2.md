# Module 2 - Timing libs, hierarchical vs flat synthesis and efficient flop coding styles

# Aim:
To perform complete functional simulation, hierarchical and flat logic synthesis, sequential cell mapping, and gate-level optimizations for combinational and sequential Verilog designs using Icarus Verilog, Yosys, and GTKWave with the Sky130 PDK library.

# Objective:
Validate RTL behavior for multiplexers and flip-flops using testbenches and GTKWave waveform analysis.
Map high-level Verilog code into physical standard cell gates using Liberty timing libraries.
Analyze structural netlists under hierarchical synthesis, flattened synthesis, and sub-module isolation modes.
Perform sequential logic mapping using dfflibmap to synthesize asynchronous and synchronous reset D flip-flops.
Apply optimization passes to demonstrate register pruning and constant propagation on tied sequential elements.


# Theory:
Timing Libraries: A Liberty timing library (.lib) defines physical parameters (area, delay, dynamic/static power, and pin capacitance) of standard cells. The synthesis tool reads this database to map generic Boolean expressions to technology-specific gates.
 Hierarchical vs. Flat Synthesis: Hierarchical synthesis preserves structural sub-module boundaries, aiding design readability and modular processing. Flat synthesis dissolves sub-module boundaries using the flatten command, allowing cross-boundary gate optimizations to reduce total area and delay.Sequential Flip-Flop Mapping: Synthesis tools infer abstract internal registers from Verilog always blocks. Commands like dfflibmap map these abstract entities to technology-specific D flip-flops. Asynchronous resets change state immediately on input transitions, whereas synchronous resets process reset conditions strictly on the active clock edge, often requiring combinational data-steering gates if dedicated library pins are absent.
 Constant Propagation: When a sequential element's data pin is permanently tied to a fixed logic value without dynamic reset overrides, its stored output remains static over time. Optimization passes identify this redundancy and eliminate the register, replacing it with a direct tied power or ground connection to save silicon area and switching power.



# Procedure:
Compile Verilog RTL modules alongside their respective testbench files using iverilog to generate simulation executables.
Execute generated binaries to output VCD files, then launch gtkwave to analyze output waveforms against clock and control signals.
Launch yosys and import the target Sky130 Liberty timing library using read_liberty.
Load target Verilog files with read_verilog and build top-level design structures via synth -top.
Synthesize multi-module designs hierarchically to export structured netlists, then apply flatten to build single-level top netlists.
Run isolated sub-module synthesis by directly declaring a child module as the top-level entity during the synthesis command.
Execute dfflibmap on sequential designs to map abstract registers into target Sky130 flip-flop standard cells.
Apply optimization and cleaning passes (opt_clean -purge) to trigger constant propagation and remove redundant sequential cells.
Run standard cell technology mapping via abc and inspect the mapped schematics using show.
# MULTIPLE MODULES SCHEMATIC
<img width="1280" height="800" alt="multipule dig" src="https://github.com/user-attachments/assets/26ec6ba3-359f-4dab-b873-13b63c894b6a" />
# MULTIPLE MODULES GTKWAVE
<img width="1280" height="800" alt="multiple wave" src="https://github.com/user-attachments/assets/49cfe2d0-97ed-4838-a94d-a009312747d8" />

# GOOD MUX SCHEMATIC
<img width="1280" height="800" alt="good mux dia" src="https://github.com/user-attachments/assets/97002cfa-7214-484b-93a0-3489d61ed28e" />
# GOOD MUX GTKWAVE
<img width="1280" height="800" alt="good mux gtkwave" src="https://github.com/user-attachments/assets/87ca5e88-40f5-4a48-8bd5-1fcab9e28062" />

# ASYNCRES SCHEMATIC
<img width="1280" height="800" alt="asyncres digr" src="https://github.com/user-attachments/assets/4fead7a3-8425-4f21-b854-ca724c6bae23" />
# ASYNCRES GTKWAVE
<img width="1280" height="800" alt="asyn wave" src="https://github.com/user-attachments/assets/800166fc-f401-47b4-862e-3096a2b5c32f" />

# SYNCRES SCHEMATIC
<img width="1280" height="800" alt="syncres dia" src="https://github.com/user-attachments/assets/ff58a9f8-43b8-4e46-9b03-2adfa0f350be" />
# SYNCRES GTKWAVE
<img width="1280" height="800" alt="syncres wave" src="https://github.com/user-attachments/assets/98c00ae7-a9fc-4907-bd41-865ea28b8160" />

# CONST SCHEMATIC
<img width="1280" height="800" alt="const dig" src="https://github.com/user-attachments/assets/2e4b4863-0055-4ef8-bb63-91c23c142f1d" />
# CONST GTKWAVE
<img width="1280" height="800" alt="const wave" src="https://github.com/user-attachments/assets/202e5417-ff94-48b4-9a74-a521f1c3e246" />


# Conclusion:
The end-to-end digital synthesis flow successfully transformed behavioral combinational and sequential RTL descriptions into verified, gate-level netlists mapped to Sky130 technology cells while demonstrating logic flattening and sequential optimization.

# Result:
RTL simulation confirmed correct behavior across all testcases. Multi-module synthesis successfully produced both structured hierarchical netlists and single-level flattened netlists. Sequential mapping correctly identified asynchronous and synchronous reset logic, mapping them to Sky130 DFF gates. Optimization passes detected constant-driven flip-flops and removed redundant registers, yielding an optimized gate-level netlist.
