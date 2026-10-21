## Real-Time Temperature Monitoring and Multi-Corner Timing Analysis Using OpenLane

## 1. Aim

To design, verify and physically implement a compact digital temperature monitoring controller, and to analyse its timing at multiple process-voltage-temperature (PVT) corners, using an open-source RTL-to-GDSII flow.

## 2. Objectives
Write synthesizable Verilog-2001 RTL for a three-level (normal / warning / alarm) temperature classifier.
Verify it with a self-checking testbench covering reset, normal, warning and alarm ranges, boundary values, enable hold and re-enable.
Synthesize the design with Yosys and check the cell count, area and absence of latches.
Confirm that the synthesized netlist behaves like the RTL using gate-level simulation (GLS).
Run the complete OpenLane flow to generate a GDSII layout with clean DRC, LVS and antenna checks.
Run OpenSTA on the routed design at all standard PVT corners and report setup and hold slack.


## 3. Real-World Application

Electronic equipment such as battery packs, power supplies, motor drives and server boards must react before a temperature becomes dangerous. A processor-free hardware block gives a response on every clock cycle, needs no software, and can drive a fan, an indicator or a shutdown signal directly.

The circuit receives an already-digitized temperature value (from an external ADC or digital sensor) and generates one of three registered status signals:

Status	Meaning	Typical action
normal	temperature below the warning threshold	none
warning	warning threshold reached	switch on fan / raise indicator
alarm	alarm threshold reached	shut down / cut power

"Real-time" here means the status is updated on every enabled clock edge, with no software in the loop.


## 4. Theory
4.1 Threshold comparison

The 8-bit unsigned input is compared with two parameterized thresholds:

normal : temp < WARN_THRESHOLD (default 40)
warning : WARN_THRESHOLD <= temp < ALARM_THRESHOLD (default 40 to 59)
alarm : temp >= ALARM_THRESHOLD (default 60)

The three conditions are mutually exclusive, so exactly one flag is high after the first enabled sample.

4.2 Registered outputs

The sampled temperature and the three flags are all computed from the same input value and stored together in flip-flops. The status therefore always corresponds to temperature_out, and the outputs are glitch-free because they come straight from registers.

4.3 Enable and reset
Synchronous active-high reset clears the temperature register and all flags to 0 (so normal is low immediately after reset).
Enable controls sampling. When enable is low, all registers keep their previous values.
4.4 Static timing analysis (STA)

For a path from a launching to a capturing flip-flop:

Setup check: data must arrive before the capture clock edge minus the setup time. setup slack = (T_clk + T_capture_clock_delay - T_uncertainty - T_setup) - T_data_arrival
Hold check: data must not change too soon after the capture edge. hold slack = T_data_arrival - (T_capture_clock_delay + T_hold)

Positive slack means the check is met. Cell delays change with process, voltage and temperature (PVT), so timing is checked at several corners: slow corners (low voltage, high temperature) normally limit setup, fast corners (high voltage) normally limit hold.

4.5 RTL-to-GDSII flow

Synthesis maps RTL to standard cells; floorplanning defines the die and power grid; placement positions the cells; clock-tree synthesis (CTS) builds the clock network; routing connects the cells with metal layers; the final checks are DRC (design rules), LVS (layout matches netlist) and antenna checks; the final output is the GDSII layout.

5. Design Specification
Item	Value
HDL	Verilog-2001
Clock	single positive-edge clock
Reset	active-high, synchronous
Warning threshold	40 (parameter WARN_THRESHOLD)
Alarm threshold	60 (parameter ALARM_THRESHOLD)

Ports

Port	Direction	Width	Description
clk	input	1	positive-edge clock
reset	input	1	synchronous reset, active high
enable	input	1	sample when high, hold when low
temperature_in	input	8	unsigned digital temperature
temperature_out	output	8	registered temperature reading
normal	output	1	temperature below warning threshold
warning	output	1	warning range
alarm	output	1	alarm range

Sequential elements: one 8-bit register for the sampled temperature and three 1-bit status registers (11 flip-flops after synthesis).
## 6. Repository Structure
.
├── rtl/
│   └── temperature_monitor.v          # RTL design
├── tb/
│   └── tb_temperature_monitor.v       # self-checking testbench
├── synthesis/
│   ├── synth.ys                       # Yosys script
│   └── temperature_monitor_synth.v    # synthesized gate-level netlist
├── openlane/
│   └── config.tcl                     # OpenLane design configuration
├── sta/
│   ├── temp_mon.sdc                   # timing constraints
│   └── summary.txt                    # worst setup/hold slack for all corners
├── results/
│   ├── final_summary_report.csv       # OpenLane summary report
│   └── temperature_monitor.gds        # final layout
├── images/                            # screenshots used in this README
└── README.md

## 7. Tools Used

Step	Tool
RTL simulation and GLS	Icarus Verilog
Waveform viewing	GTKWave
Logic synthesis	Yosys
RTL-to-GDSII flow	OpenLane (v0.21, run in Docker)
Layout viewing and DRC	Magic
Static timing analysis	OpenSTA


## 8. Procedure
## 8.1 RTL simulation
bash
iverilog -g2001 -o rtl_sim temperature_monitor.v tb_temperature_monitor.v
vvp rtl_sim
gtkwave wave.vcd

The testbench drives inputs on the falling clock edge and checks outputs 1 ns after the rising edge, which avoids race conditions. Test cases:

Test	Inputs	Expected
Reset	reset high with enable high and input 100	all outputs 0
Normal range	0, 25, 39	normal = 1
Warning range	40, 45, 59	warning = 1
Alarm range	60, 75, 255	alarm = 1
Boundaries	39, 40, 59, 60	correct flag at each edge
Enable hold	enable low, input changed to 10 and 200	outputs unchanged
Re-enable	enable high, 200 then 10	outputs follow input
Mid-run reset	reset during operation	outputs cleared
Mutual exclusion	every check	never more than one flag high
# 8.2 Synthesis (Yosys)
bash
yosys -l yosys.log synth.ys

Script: read the RTL, synth -top temperature_monitor -flatten, map flip-flops (dfflibmap) and logic (abc) to the SKY130 HD typical-corner library, then report cell statistics and write the netlist.

# 8.3 Gate-level simulation

The synthesized netlist is simulated with the same testbench (without the parameter override, since the netlist has no parameters) and the standard-cell models:

bash
iverilog -g2012 -DFUNCTIONAL -DUNIT_DELAY=#0 -o gls_sim \
  primitives.v sky130_fd_sc_hd.v temperature_monitor_synth.v tb_gls.v
vvp gls_sim
# 8.4 Physical design (OpenLane)

Design configuration (config.tcl):

tcl
set ::env(DESIGN_NAME) "temperature_monitor"
set ::env(VERILOG_FILES) "$::env(DESIGN_DIR)/src/temperature_monitor.v"
set ::env(CLOCK_PORT) "clk"
set ::env(CLOCK_PERIOD) "10.0"
set ::env(FP_CORE_UTIL) 30
set ::env(PL_TARGET_DENSITY) 0.5

Run inside the OpenLane Docker container:

bash
./flow.tcl -design temperature_monitor -tag run8 -overwrite

The flow performs synthesis, floorplan, power grid, placement, CTS, routing, parasitic extraction, STA, GDS generation, Magic DRC, LVS and antenna checks.

# 8.5 Multi-corner STA (OpenSTA)

Constraints used (temp_mon.sdc):

tcl
create_clock -name clk -period 10 [get_ports clk]
set_clock_uncertainty 0.25 [get_clocks clk]
set_input_delay 2 -clock clk [get_ports {reset enable temperature_in*}]
set_output_delay 2 -clock clk [get_ports {temperature_out* normal warning alarm}]

For each of the 16 SKY130 HD corner libraries (tt, ss and ff, excluding the ccsnoise variants), OpenSTA reads the library, the netlist, the SDC and the routed SPEF, then reports setup and hold:

tcl
read_liberty <corner>.lib
read_verilog temperature_monitor.synthesis_preroute.v
link_design temperature_monitor
read_sdc temp_mon.sdc
read_spef temperature_monitor.spef
set_propagated_clock [all_clocks]
report_checks -path_delay max
report_checks -path_delay min
report_worst_slack -max
report_worst_slack -min
# 9. Results
# 9.1 RTL simulation waveform
<img width="1920" height="922" alt="image" src="https://github.com/user-attachments/assets/491df603-2433-4f7c-b1d8-fb1a26421356" />



The waveform shows temperature_out as unknown (xx) until the first reset edge, then 00. Inputs 0x19 (25) and 0x27 (39) raise normal; 0x28 (40) to 0x3B (59) raise warning; 0x3C (60), 0x4B (75) and 0xFF (255) raise alarm. While enable is low the outputs hold their previous values, and the three flags are never high together.

# 9.2 RTL and gate-level simulation comparison

<img width="1920" height="922" alt="image" src="https://github.com/user-attachments/assets/1a518a19-1b3e-4e6c-a98d-bf2c61369f91" />


Both simulations print ALL TESTS PASSED (23 checks, 0 errors), and a diff of their PASS/FAIL logs reports "RTL and GLS results are IDENTICAL". This shows the synthesized netlist behaves like the RTL.

# 9.3 Synthesis statistics

<img width="1920" height="922" alt="image" src="https://github.com/user-attachments/assets/6aefb81d-b581-48b4-ab4a-de943c425086" />


Item	Result
Total cells	38
Flip-flops (dfxtp_1)	11
2-to-1 multiplexers (mux2_1)	8 (enable hold)
Chip area	404.14 µm²
Sequential area	220.21 µm² (54.49 %)
Latches	none
# 9.4 OpenLane flow completion

<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/25b52d79-1f16-4629-a337-045b28f663ab" />


The flow completed without fatal errors in about 2 minutes. Final checks:

Check	Result
Magic DRC	0 violations
LVS	no net, device, pin or property mismatches (0 errors)
Antenna	0 pins and 0 nets violated
# 9.5 Placement

<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/4e846091-ca79-4323-8205-6e592131335f" />

<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/77494a43-a0ff-4a89-97ec-ac0e9a5fd6ec" />


# 9.6 Post-route layout
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/a02805d7-d99c-468d-b2ee-07ef6a5af1ea" />


<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/9726cd02-79ae-49cc-bc1a-6cf34373ce4d" />


# 9.7 Final GDSII layout

<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/b5ecabd7-aed2-4ab1-a843-bf7421f6102c" />
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/ed4dfaab-2375-4636-80c6-bbe14ea1d766" />

Note on DRC: the signoff DRC is the batch Magic DRC in the OpenLane flow, which reported 0 violations (section 9.4). The violation counter in the toolbar of an interactive Magic session can differ because it depends on the DRC style and hierarchy loaded in that session.

# 9.8 Multi-corner timing summary
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/f0d712a4-29ad-4cd4-b90e-2f0b1af87028" />



Corner	Worst setup slack (ns)	Worst hold slack (ns)
ff_100C_1v65	7.1636	0.1655
ff_100C_1v95	7.2744	0.0797
ff_n40C_1v56	6.9946	0.2306
ff_n40C_1v65	7.1213	0.1756
ff_n40C_1v76	7.1980	0.1262
ff_n40C_1v95	7.2842	0.0667
ss_100C_1v40	5.4474	1.2982
ss_100C_1v60	6.2904	0.8579
ss_n40C_1v28	-0.5449	1.5114
ss_n40C_1v35	2.3954	1.5672
ss_n40C_1v40	3.6063	1.5923
ss_n40C_1v44	4.2781	1.3722
ss_n40C_1v60	5.7699	0.8607
ss_n40C_1v76	6.4487	0.5822
tt_025C_1v80	7.0395	0.2827
tt_100C_1v80	7.0345	0.2999

Constraints: 10 ns clock, 0.25 ns clock uncertainty, 2 ns input and output delays, and a single nominal-corner SPEF used for all corners.

# 9.9 Setup violation at the extreme low-voltage corner
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/7e06405d-0875-41ec-87b1-923ed36b824b" />



At ss_n40C_1v28 (1.28 V, -40 C, slow silicon) the worst setup path (from temperature_in[4] through the comparator logic to a status flip-flop) misses timing by 0.5449 ns (slack -0.5449 ns). The or4_4 gate alone contributes about 5.1 ns in this path at that voltage. All other corners, including the typical, slow (ss_100C_1v60) and fast (ff_n40C_1v95) corners, meet setup and hold.
# proof:
<img width="1920" height="922" alt="image" src="https://github.com/user-attachments/assets/6b166946-74c0-497f-a140-84f85ec78afc" />

# 10. Observations and Limitations
Hold is met at all 16 corners; the smallest hold margin is 0.0667 ns at ff_n40C_1v95.
Setup is met at 15 of 16 corners; the violated corner (ss_n40C_1v28) is an extreme low-voltage stress corner. A longer clock period or a faster implementation of the comparator would be needed to meet it.
The design takes an already digitized temperature; the sensor or ADC is external to the circuit.
The thresholds are Verilog parameters (fixed at synthesis time), not runtime-programmable registers.
OpenLane produces one parasitic extraction (SPEF), so all corners use the same RC values with different cell libraries. This is a common simplification, not fully corner-specific extraction.
Timing results depend on the constraints listed in section 9.8.
In early OpenLane runs, global placement diverged when the die was fixed at a large size for this very small design. Sizing the die from utilization (FP_CORE_UTIL 30) resolved this.
# 11. Conclusion

A compact temperature monitoring controller was designed in Verilog, verified with a self-checking testbench (23 checks, 0 errors), synthesized to 38 cells and 404.14 µm², confirmed by gate-level simulation, and taken through the OpenLane flow to a GDSII layout with 0 DRC violations, clean LVS and no antenna violations. Multi-corner STA at 16 corners shows hold met everywhere and setup met at every corner except the extreme ss_n40C_1v28 corner.

# 12. Future Scope
Programmable thresholds through a configuration register interface.
Hysteresis on the warning and alarm thresholds to avoid chattering near the limits.
Adding a temperature-averaging filter to reduce sensor noise.
Optimizing the comparator logic to meet setup at the ss_n40C_1v28 corner.
Corner-specific parasitic extraction for a more complete multi-corner signoff.
