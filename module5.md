# Module 5: Optimization in Synthesis
# Aim:
To analyze, simulate, and synthesize combinational digital circuits using Verilog HDL, evaluating the hardware behavior of complete versus incomplete conditional constructs (if, case, multiplexers, demultiplexers, comparators, and adders) and observing latch inference versus clean combinational logic using Icarus Verilog, GTKWave, and Yosys synthesis with the SkyWater 130nm standard cell library.

# Objectives:
Differentiate between pure combinational gate structures and unintended level-sensitive transparent latches (DLATCH).
Perform functional simulation of basic combinational blocks (Mux, Demux, Comparator, Ripple Carry Adder) alongside incomplete conditional logic.
Visualize signal transitions and output hold states using GTKWave VCD traces.
Synthesize digital designs with Yosys using the Sky130 library (sky130_fd_sc_hd__tt_025C_1v80.lib) to inspect technology-mapped schematics.

# Theory:
Incomplete Conditional Statements & Latch Inference
In combinational always @(*) blocks, every possible execution path must explicitly define an output value.
Incomplete IF Statements: Omission of an else path forces the synthesis tool to infer a level-sensitive transparent latch to preserve the prior output state when the condition evaluates to false.
Incomplete Case Statements / Bad Case: Omitting binary selection permutations without specifying a default clause leaves unhandled states undefined, resulting in latch inference.
Standard Combinational Logic Modules

Multiplexer (MUX): Selects one of several input signals and forwards it to a single output line based on control inputs.
Demultiplexer (DEMUX): Takes a single input signal and routes it to one of several output lines depending on selection signals.
Comparator (COMP): Compares two binary numbers to determine equality (A = B), greater-than (A > B), or less-than (A < B) relationships.
Ripple Carry Adder (RCA): Cascades full adders to perform multi-bit binary addition by passing the carry output of each stage to the carry input of the next.

# Procedure:
RTL Source Entry: Write Verilog designs for complete combinational modules (mux, demux, comparator, rca) and incomplete conditional modules (inferred_latch / Incomplete IF 1 & 2, bad_case / Incomplete Case).
Testbench Execution: Compile designs with matching testbenches using iverilog <design.v> <tb_design.v>.
Simulation Output: Run ./a.out to generate .vcd trace files.
Waveform Analysis: View waveform timing diagrams using gtkwave <file.vcd> &.
Logic Synthesis (Yosys):
Launch yosys in the terminal.
Read the technology library: read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib.
Read design: read_verilog <design.v>.
Run synthesis: synth -top <top_module>.
Map gates: abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib.
Display gate netlist: show <top_module>.
Topics Covered & Image Insertion Details

 # Topic 1: Incomplete IF Statement - Case 1
Evaluates basic enable-controlled assignment. When enable is high, output follows input. When enable drops, the lack of an else branch freezes the output, demonstrating level-sensitive latch retention.
# incomp_if1.v
<img width="1280" height="800" alt="incomp code" src="https://github.com/user-attachments/assets/cacc09a1-3496-4002-9fff-113b2409bd6a" />

# Synthesized Netlist Schematic of incomp_if1
<img width="1280" height="800" alt="incomp diagram" src="https://github.com/user-attachments/assets/84221ff7-1b27-4d49-b3ec-aac3bbc27830" />

# GTKWave Waveform Simulation of 8-bit incomp_if1
<img width="1280" height="800" alt="gtkwave tb incomp" src="https://github.com/user-attachments/assets/4b04cb72-93fc-4d36-84f9-f8e2f934873b" />


# Topic 2: Incomplete IF Statement - Case 2
Evaluates multi-variable or nested if statements missing final else execution paths. Simulation demonstrates that failing to cover all conditional combinations locks the output in place whenever condition checks fail.
# incomp_if2.v
<img width="1280" height="800" alt="incomp code2" src="https://github.com/user-attachments/assets/77cc6f38-1452-48a9-9485-e82dd7f1bd5a" />
# Synthesized Netlist Schematic of incomp_if2
<img width="1280" height="800" alt="incomp2 digram" src="https://github.com/user-attachments/assets/e3eccbde-5917-4944-9269-8a800d80cb8e" />
# GTKWave Waveform Simulation of 8-bit incomp_if2
<img width="1280" height="800" alt="gtkwave tb incomp2" src="https://github.com/user-attachments/assets/c06b8c0a-73cb-479d-b842-7ace896b6fdd" />





# Topic 3: Incomplete Case Statement / Bad Case
Models a multiplexer structure where select state 2'b11 and the default fallback statement are omitted. Transitioning to 2'b11 stops output updates, keeping the signal latched at its previous value.
# Synthesized Netlist Schematic of bad_case
<img width="1280" height="800" alt="bad case" src="https://github.com/user-attachments/assets/ed96cbb9-83cd-413e-802a-ac14efa9219d" />
# GTKWave Waveform Simulation of 8-bit BAD CASE
<img width="1280" height="800" alt="wave bad case" src="https://github.com/user-attachments/assets/99d09780-7e7a-4270-ae55-42873ab6b4e7" />


# Topic 4: Multiplexer (MUX) Analysis
Models complete selection logic where all input selector permutations map cleanly to an output. Synthesis confirms clean combinational logic gates without latch cell instantiation.
<img width="1280" height="800" alt="mux code" src="https://github.com/user-attachments/assets/42826ef8-186d-4927-a1c9-0d487b7a6198" />
# Synthesized Netlist Schematic of MUX
<img width="1280" height="800" alt="mux dia" src="https://github.com/user-attachments/assets/33baf8ac-2297-4bfd-bb0a-e1aaafd320a1" />
# GTKWave Waveform Simulation of 8-bit MUX
<img width="1280" height="800" alt="mux gtkwave" src="https://github.com/user-attachments/assets/490addab-48c7-4d22-923d-826762a65edf" />



# Topic 5: Demultiplexer (DEMUX) Analysis
Evaluates signal distribution from a single input to multiple output lines based on selector inputs, verifying pure combinational AND/OR gate mapping in synthesis.
# Synthesized Netlist Schematic of DEMUX
<img width="1280" height="800" alt="demux dig" src="https://github.com/user-attachments/assets/428bf5d3-693a-4f81-8812-834fc1eac7ca" />
# GTKWave Waveform Simulation of 8-bit DEMUX
<img width="1280" height="800" alt="demux wave" src="https://github.com/user-attachments/assets/a8b292a8-d6ac-4a40-a190-42de92b75bf9" />




# Topic 6: Magnitude Comparator (COMP) Analysis
Evaluates magnitude comparison between two binary numbers. Complete conditional coverage ensures gate mapping using standard XNOR and AND logic trees.
# Synthesized Netlist Schematic of COMP
<img width="1280" height="800" alt="comp diagram" src="https://github.com/user-attachments/assets/63abaab6-d441-4f20-8e3b-05cde43aa8a0" />
# GTKWave Waveform Simulation of 8-bit COMP
<img width="1280" height="800" alt="gtkwave tb comp" src="https://github.com/user-attachments/assets/6658d0a2-1654-44b1-a7fe-7cc2a7303fcf" />



# Topic 7: Ripple Carry Adder (RCA) Analysis
Evaluates multi-bit binary addition by cascading single-bit full adder units, illustrating carry ripple behavior during timing analysis and gate-level synthesis.
# rca.v
<img width="1280" height="800" alt="rca code" src="https://github.com/user-attachments/assets/869b66f9-d9c9-4328-bab8-c14330a70902" />
# Synthesized Netlist Schematic of RCA
<img width="1280" height="800" alt="rca dig" src="https://github.com/user-attachments/assets/3bf849bc-dfc8-4d88-af73-32ba53ec8b18" />
# GTKWave Waveform Simulation of RCA
<img width="1280" height="800" alt="rca wave" src="https://github.com/user-attachments/assets/367a3ea4-7d18-435d-a6db-7db9ed8c0e76" />



# Topic 8: Synthesis & Gate-Level Netlist Verification
Synthesis of fully specified modules (mux, demux, comparator, rca) produces clean combinational standard cell gates (sky130_fd_sc_hd__and*, or*, mux*). In contrast, synthesis of inferred_latch and bad_case confirms explicit instantiation of transparent latch cells (



# Conclusion:
Unintended latch inference in combinational Verilog blocks leads to race conditions, timing violations, and unnecessary power dissipation in digital ASIC backend flows. Complete conditional logic must always be enforced by providing complementary else branches, exhaustive default clauses in case statements, and explicit signal definitions across all execution paths.


# Results:
Functional simulation verified that incomplete conditional constructs hold prior output values during unhandled input states.
Fully specified combinational modules (Mux, Demux, Comparator, RCA) executed with zero output hold delay and mapped purely to combinational logic gates.
Yosys technology synthesis confirmed the physical inference of level-sensitive transparent latches (DLATCH) whenever else branches or case default conditions were omitted.

