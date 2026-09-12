## SKY130 CMOS Inverter Design, SPICE Simulation, Layout and Parasitic Extraction


## Aim
To design and analyze a CMOS inverter using SkyWater SKY130 technology, perform SPICE simulation and switching analysis, create and verify the physical layout using Magic, and extract the circuit information from the layout. 

## Project Overview
This project covers the Module 3 SKY130 VLSI laboratory flow.
The work is divided into three parts:
SKY130_03_SK1 – Labs for CMOS Inverter
SKY130_03_SK2 – Inception of Layout
SKY130_03_SK3 – SKY130 Technology File Labs
The project starts with CMOS inverter concepts and SPICE simulation, then proceeds to CMOS layout formation, Magic layout verification, extraction and correction of layout-rule errors. 

## Objectives
 Understand the operation of a CMOS inverter.
Create a transistor-level SPICE netlist.
Run the circuit using ngspice.
Obtain input/output voltage characteristics.
Find the switching threshold voltage, Vm.
Study static and dynamic behavior.
Understand SKY130 CMOS layout layers.
Build and inspect a layout using Magic.
Extract a SPICE netlist from the layout.
Identify and fix layout-rule errors.
Maintain the project commands and results in GitHub. 


# SKy130_03_SK1 – Labs for CMOS Inverter
# 10-Placer Revision
This topic revises the basic concepts required before performing the CMOS inverter laboratory.
Important concepts are:
PMOS and NMOS
CMOS inverter
VDD and GND
Input and output nodes
Pull-up network
Pull-down network
Logic 0 and Logic 1
Voltage Transfer Characteristic (VTC) 
SKY130_CMOS_Inverter_GitHub_Project_Report.md
CMOS Inverter
A CMOS inverter consists of a PMOS transistor and an NMOS transistor.
PMOS provides the pull-up path.
NMOS provides the pull-down path.
Input is connected to both transistor gates.
Output is taken from the connected drains.
PMOS is connected toward VDD.
NMOS is connected toward GND.
# SPICE Deck Creation for CMOS Inverter
A SPICE deck is a text file containing the circuit description, transistor/device information, supply, input stimulus and simulation commands. 
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/eccd792e-7741-4eb2-94a3-4248df4f0517" />

# Command
vim sky130inv.spice
After editing in Vim:
Esc
:wq
Enter

<img width="1920" height="922" alt="image" src="https://github.com/user-attachments/assets/5018ccc7-4d53-4c5e-9c36-531a538b5e1f" />


Caption:
Figure 1: Creation of the SKY130 CMOS inverter SPICE deck.
Topic 3: SPICE Simulation Lab for CMOS Inverter
After creating the SPICE deck, the circuit can be simulated using ngspice.

# Command
ngspice sky130inv.spice
Inside ngspice:
run
plot v(in) v(out)
To exit:
quit
The expected inverter behavior is:
Low input → High output
High input → Low output
Transition region around the switching threshold 

<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/31190361-9690-42db-b631-247f1bcb3def" />

Caption:
Figure 2: SKY130 CMOS inverter SPICE simulation result.
## Switching Threshold – Vm
Vm is the input voltage at which the CMOS inverter changes between its logic states.
It is commonly identified near the point where:
Vin ≈ Vout
The exact Vm depends on transistor sizing, supply voltage and process parameters. 

Record your actual value
VDD = __ V
Vm  = __ V
Do not enter a guessed Vm value.

## Static and Dynamic Simulation
Static Simulation
Static simulation studies the DC behavior of the inverter.
Main outputs:
Voltage Transfer Characteristic (VTC)
Switching threshold
Logic-high region
Logic-low region
Dynamic Simulation
Dynamic simulation studies the inverter response when the input changes with time.
Important parameters:
Propagation delay
Rise time
Fall time
Output waveform 

# Typical transient command
.tran 0.1n 100n
Run:
ngspice sky130inv.spice

## SKy130_03_SK2 – Inception of Layout
# Create Active Regions
The active region is the semiconductor area where transistor source and drain regions are formed.
For the CMOS inverter:
NMOS has its active region.
PMOS has its active region.
Active regions define where the transistor source and drain can be formed. 

<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/84dba0c7-d96f-49ea-b776-97940ae5c43e" />

Caption:
Figure 3: Selection/inspection of the NMOS region during SKY130 layout creation.
# Formation of N-Well and P-Well
N-Well
The N-well is the region used to accommodate PMOS devices.
P-Well
The P-well is the region used to accommodate NMOS devices, depending on the process structure.
Wells provide the appropriate body/substrate environment for MOS transistors. 

# Formation of Gate Terminal
The gate is formed where polysilicon crosses the active region.
Polysilicon + Active region crossing
                ↓
          MOS transistor
The gate controls conductivity between the source and drain. 

<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/7e509893-e0ea-4169-b56d-e1df54117d63" />

Caption:
Figure 4: Layout node/connection selection during CMOS inverter layout formation.
# Lightly Doped Drain (LDD)
LDD means Lightly Doped Drain.
It is a lightly doped region near the drain/source side of a MOS transistor.
Purpose
Reduces high electric field near the drain.
Improves device reliability. 

# Source–Drain Formation
Source and drain regions are formed using appropriate implantation/doping.
For the CMOS inverter:
PMOS source → VDD
NMOS source → GND
PMOS and NMOS drains → output node 


<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/9f2d1b1c-02f4-4f9f-a429-6ad8c5798dfe" />

Figure 5: VDD/power connection in the CMOS inverter layout.
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/8dafc948-d5b1-42e6-888d-b1f4e16c6efe" />

Caption:
Figure 6: NMOS GND connection in the CMOS inverter layout.
# Local Interconnect Formation
Local interconnect is used to connect nearby device terminals and layout regions.
It provides connectivity between transistor terminals while reducing the need for longer metal connections. 

# Higher-Level Metal Formation
Higher metal layers are mainly used for:
Power distribution
Signal routing
Connections between different regions
Generally:
Lower layers
     ↓
Local/device connections

Higher layers
     ↓
Longer signal and power connections
Actual SKY130 layer names should be taken from the installed PDK/lab environment. 

# SKY130 Basic Lab Introduction
The SKY130 PDK provides:
Device models
Layer definitions
Design rules
Technology information
Standard-cell-related information
This allows the circuit to be designed for a real semiconductor manufacturing process.
<img width="1920" height="922" alt="image" src="https://github.com/user-attachments/assets/4170710f-a993-4082-ab8f-128e79b65e75" />

# Create Standard-Cell Layout
A standard cell is a reusable layout block with defined dimensions and power connections.
A CMOS inverter standard-cell layout normally contains:
PMOS
NMOS
VDD rail
GND rail
Input connection
Output connection
Contacts
Required metal layers 

5.3 SKy130_03_SK3 – SKY130 Technology File Labs
# Topic 1: Create Final SPICE Netlist
The final SPICE netlist represents the circuit after the required device and layout information has been prepared.
Command
ngspice sky130inv.spice


<img width="1920" height="922" alt="image" src="https://github.com/user-attachments/assets/6bdf5fce-72de-4d55-a5e0-71e67fca4b6f" />

# Modified/final SKY130 CMOS inverter SPICE deck.
# Topic 2: Characterize the Inverter
Characterization determines important electrical parameters such as:
Switching threshold
Propagation delay
Rise time
Fall time
Power-related behavior
Only values obtained from the actual simulation should be recorded. 

# Topic 3: Introduction to Magic
Magic is a VLSI layout tool used for:
Viewing layout layers
Editing layout
Design Rule Checking (DRC)
Inspecting connections
Extracting circuit information 

General command
magic -T <technology-file> <layout-file>
Use the exact technology-file path available in your SKY130 PDK. 
# Topic 4: SKY130 PDK Introduction
The SKY130 PDK provides the technology information required by the design tools.
It contains:
Technology/layer information
Device models
Design rules
Physical-design information


# Topic 5: Magic and SPICE
The basic extraction flow is:
Layout
   ↓
DRC / Inspection
   ↓
Extraction
   ↓
SPICE Netlist
   ↓
SPICE Simulation
Magic can inspect the layout and extract a circuit representation from it. 

<img width="1920" height="922" alt="image" src="https://github.com/user-attachments/assets/5d08b3bf-90d3-4f5c-8310-09873fa08fd1" />

Completion of SPICE extraction from the SKY130 CMOS inverter layout.
# Topic 6: Fixing poly.9 Error
A DRC error means that a layout design rule has been violated.
Procedure
Open the layout in Magic.
Run DRC.
Locate the highlighted error.
Read the exact rule message.
Modify the geometry according to the SKY130 rule.
Run DRC again.
Repeat until the required errors are cleared.
An error should not simply be hidden by deleting a layer. 

# Topic 7: Implementing Poly Connections
Polysilicon is mainly used for MOS gates.
While making poly connections:
Maintain required spacing.
Avoid unintended transistor formation.
Use the correct contact/interconnect structure.



# NMOS layout/device verification during SKY130 layout implementation.

<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/c4a7b440-213d-4a76-9f4d-2e59e1d4c4cc" />

# PMOS layout/device verification during SKY130 layout implementation.
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/a7e5642e-d5d7-4ade-a02e-950fff9d8d8f" />

# Topic 8: Magic Challenge – Missing Connection
A missing connection can result in:
Incorrect extracted netlist
LVS mismatch
Floating node
Incorrect circuit behavior
The following should be checked:
Contacts
Metal continuity
Poly continuity
Source/drain connections
VDD/GND connections 

# Procedure

CMOS Inverter Concepts
        ↓
SPICE Deck Creation
        ↓
SPICE Simulation
        ↓
Switching Threshold (Vm)
        ↓
Static & Dynamic Simulation
        ↓
Active Region Formation
        ↓
N-Well / P-Well
        ↓
Gate Formation
        ↓
LDD Formation
        ↓
Source / Drain Formation
        ↓
Local Interconnect
        ↓
Higher-Level Metal
        ↓
Standard-Cell Layout
        ↓
Final SPICE Netlist
        ↓
Inverter Characterization
        ↓
Magic Layout Inspection
        ↓
SKY130 PDK
        ↓
SPICE Extraction
        ↓
DRC / poly.9 Error Fix
        ↓
Poly Connections
        ↓
Missing Connection Check
# Result

CMOS Inverter
      ↓
SPICE Netlist
      ↓
ngspice Simulation
      ↓
Vm / VTC / Timing
      ↓
SKY130 Layout
      ↓
Magic DRC
      ↓
SPICE Extraction
      ↓
Layout Verification
The actual Vm, delay, rise time, fall time and DRC results should be entered only from your real laboratory outputs. 

8. Conclusion
The SKY130 project provides practical understanding of the CMOS inverter design flow. It covers SPICE simulation, switching-threshold analysis, static and dynamic behavior, SKY130 layout formation, Magic-based layout inspection, DRC error correction, poly connections, missing-connection checking and SPICE extraction. The saved screenshots provide evidence of the simulation, layout and extraction activities.
