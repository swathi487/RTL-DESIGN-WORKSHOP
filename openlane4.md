# SKY130 – Pre-Layout Timing Analysis using OpenSTA and OpenLane
# Aim
To understand and perform pre-layout timing analysis of a digital design using SKY130 technology, timing libraries, delay tables, synthesis, OpenSTA and clock-tree concepts.
# Project Overview
This module explains how timing information is prepared and used before physical layout. The flow includes:
Timing modelling
Timing libraries
Delay tables
Synthesis
Setup and hold timing analysis

# Project Overview
This project focuses on pre-layout timing analysis using the SKY130 PDK. It explains how timing information is modelled using standard-cell libraries and delay tables, followed by synthesis and static timing analysis using OpenSTA. The project also covers ideal-clock timing analysis, Clock Tree Synthesis (CTS), clock buffering, crosstalk, and real-clock setup and hold analysis. Through these steps, the timing performance and possible timing violations of a digital design can be studied and improved.

# procedure
Matter:
This module explains pre-layout timing analysis using SKY130 technology, including timing libraries, delay tables, synthesis, OpenSTA, clock-tree synthesis and real-clock timing analysis.


Convert Grid Information to Tracks

Grid and track information is required for physical design and routing. The technology information is used to define the routing tracks.

<img width="1920" height="922" alt="image" src="https://github.com/user-attachments/assets/2135e61d-8f53-4be9-85be-f7554040040f" />

Then:
Track information can be inspected using the technology and LEF-related files.

<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/96edbbb4-d891-4cd1-a273-e424562e89bc" />


Magic is used to view and work with layout information. TkCon is used to enter commands and interact with Magic.
<img width="1920" height="922" alt="image" src="https://github.com/user-attachments/assets/f0580d1d-036a-4049-be8f-f46f9efb9c16" />

Then, if you explain the expanded TkCon screen:
<img width="1920" height="922" alt="image" src="https://github.com/user-attachments/assets/83b45dff-764b-4c1f-9ab0-2658bbebc084" />

# Timing Libraries
Timing Analysis with Real Clocks
Setup Timing Analysis Using Real Clock
Unlike ideal-clock analysis, real-clock analysis considers the actual clock network.
The timing path includes:
Clock Source
     ↓
Clock Tree
     ↓
Launch Flip-Flop
     ↓
Combinational Logic
     ↓
Capture Flip-Flop

First: fast lib
<img width="1920" height="922" alt="image" src="https://github.com/user-attachments/assets/1dfe9e01-576c-4803-baf4-986897295a03" />

Second: typical lib
<img width="1920" height="922" alt="image" src="https://github.com/user-attachments/assets/70e013e2-23db-40a1-8a59-cb8dcd0eac0a" />

Third: slow lib
<img width="1920" height="922" alt="image" src="https://github.com/user-attachments/assets/964e8379-04c2-441b-bc6e-d7196f3d6751" />

 # Library Information
After this matter:
LEF files contain physical information about standard cells and are used during physical design.
<img width="1920" height="922" alt="image" src="https://github.com/user-attachments/assets/0adc3bc8-cf47-4430-a174-a12630b48192" />


# Configure Synthesis
Synthesis converts the RTL design into a gate-level netlist using the selected standard-cell library.
#  Commands:
yosys
read_verilog <design>.v
read_liberty -lib <library>.lib
synth -top <top_module>
stat
<img width="1920" height="922" alt="image" src="https://github.com/user-attachments/assets/da5616aa-a2c4-4ffb-802f-97a9c18cc54c" />

Then:
The synthesized design can be inspected to verify the generated cells and design statistics.
<img width="1920" height="922" alt="image" src="https://github.com/user-attachments/assets/4453766a-d362-4120-b8d4-8bcb916bb6cf" />

# Timing Analysis with Ideal Clocks 
Setup Timing Analysis
After explaining setup timing:
Setup timing verifies whether data reaches the destination flip-flop within the required time before the clock edge.
Commands:
read_liberty <library>.lib
read_verilog <design>.v
link_design <top_module>
read_sdc <constraints>.sdc
report_checks -path_delay max

Clock Jitter and Uncertainty
After:
Clock jitter represents variation in clock arrival time. Clock uncertainty provides a timing margin for such variations.
Command:


# OpenSTA Configuration
After:
read_liberty <library>.lib
read_verilog <design>.v
link_design <top_module>
read_sdc <constraints>.sdc
report_checks

 Clock Tree Synthesis
# Placement
After:
Before CTS, the design undergoes placement, where standard cells are positioned in the layout.
<img width="1920" height="922" alt="image" src="https://github.com/user-attachments/assets/4f7a04d3-a23f-477e-82dd-a3a0ae0a042a" />

Then:
The placement can be inspected in greater detail.
<img width="1920" height="922" alt="image" src="https://github.com/user-attachments/assets/2d534c6c-f348-429b-927d-3a1c25834dce" />

For an enlarged view:
<img width="1920" height="922" alt="image" src="https://github.com/user-attachments/assets/cbc3d036-bdeb-4abc-946b-954733c28880" />
<img width="1920" height="922" alt="image" src="https://github.com/user-attachments/assets/e333c89d-bd7a-42ae-8031-0c9050a3373c" />


# Result
The SKY130 pre-layout timing flow was studied successfully. Timing libraries and delay tables were understood, synthesis was configured, and setup/hold timing analysis was performed conceptually using OpenSTA. Clock-tree synthesis and the difference between ideal-clock and real-clock timing analysis were also studied.
# Conclusion
This module provides an understanding of how timing information is generated and used before and after clock-tree implementation. The use of Liberty timing libraries, delay tables, synthesis, OpenSTA and CTS helps identify timing problems and improve the overall performance of a digital design.
