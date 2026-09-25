# Static Timing Analysis of VSD BabySoC Using OpenSTA and SKY130 HD Libraries


# 1. Introduction
Static Timing Analysis (STA) is a method used to evaluate the timing performance of a digital circuit without requiring functional simulation. It checks the propagation of signals between timing points and determines whether the design satisfies its required setup and hold constraints.
In this work, the VSD BabySoC design is analyzed using OpenSTA with SKY130 HD standard-cell timing libraries. The analysis is performed across multiple process, voltage, and temperature (PVT) corners to study the timing behavior of the design.

# 2. Design and STA Files
The VSD BabySoC Static Timing Analysis uses several important design and timing files. The synthesized netlist contains the gate-level representation of the BabySoC design. The SDC file defines the clocks and timing constraints required for analysis. The PLL and DAC library files provide timing information for the corresponding blocks. The SKY130 HD timing libraries provide standard-cell delay and timing characteristics for different PVT conditions. These files are loaded into OpenSTA to perform the complete multi-corner timing analysis of the design.

# 3.Analysis Methodology
OpenSTA is used to analyze the VSD BabySoC timing using the synthesized netlist, SDC constraints, and SKY130 HD libraries. Setup and hold checks are performed at different PVT corners. The timing reports are analyzed using arrival time, required time, slack, WNS, and TNS to identify timing violations.

# 4. Timing Analysis Results
The timing results obtained from OpenSTA are examined for different PVT corners. The setup and hold slack values are used to determine whether the design meets the required timing constraints. Positive slack indicates that the timing requirement is satisfied, while negative slack indicates a timing violation.


# 5.Setup and Hold Timing Analysis
Setup and hold checks are performed to verify the correct timing operation of the VSD BabySoC. The setup analysis checks whether data reaches the destination before the required clock edge, while hold analysis checks whether data remains stable after the clock edge. The slack values from different PVT corners are used to identify timing margins and violations.

# Figure1-OpenSTA Timing Analysis
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/1b666182-8b4b-4fb7-9cb2-269a3e33b009" />

Description: The screenshot shows an OpenSTA timing analysis performed in the terminal. It displays the clock path, data arrival time, data required time, and calculated slack, demonstrating how OpenSTA checks the timing performance of the digital circuit.

# Figure2-SDC Timing Constraint File
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/3899d0d6-8048-4dce-81e9-d0305f21ec77" />


Description: The screenshot shows the SDC timing constraint file being edited in the terminal. It defines the timing units and creates the main clock with a specified clock period, which is used by OpenSTA for timing analysis.














# Figure3-OpenSTA Multi-Library Run Script
The run script loads the minimum and maximum timing libraries, custom PLL and DAC libraries, the synthesized Verilog netlist, and SDC timing constraints. These files are then used by OpenSTA to perform multi-corner timing analysis.
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/228cc66e-57b9-4351-aa2d-1b75ecabb4d5" />

Description: The script loads the required timing libraries, netlist, and SDC constraints before performing timing checks.


















Figure 4 – SKY130 HD Timing Library files
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/fb7c08aa-28ae-410e-93c0-1ee4c42bb710" />

Description: The screenshot shows the SKY130 HD timing library files in the file manager. These library files provide standard-cell timing information for different PVT conditions and are used by OpenSTA during Static Timing Analysis.







Figure 5 – SKY130 HD Timing Library
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/0a937583-cdd0-4831-9d98-737a50906ae0" />

Description: The screenshot shows a SKY130 HD timing library file being viewed in a text editor. It contains timing information such as pin direction, maximum transition, and capacitance values used by OpenSTA during timing analysis.




















# Figure 6- Multi-Corner STA Report Generation
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/6a41b981-0343-4477-a026-7aefcb03dba4" />

Description: The screenshot shows the generation of multi-corner STA reports using OpenSTA. Timing reports are created for different PVT corners, and the terminal confirms that all STA commands have been completed successfully.



# Figure7-Synthesis and Technology Mapping

<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/61d233e9-fec4-4b6f-bcc0-f609e52eddaa" />
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/6071b11b-7fa5-461d-9293-213bda549770" />

<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/1d9a8929-0843-4ad2-b20d-d1c17ab99241" />

<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/36b6694c-4e1d-4640-9a00-af12c60eeab0" />



Description:This screenshot shows the successful execution of the Yosys synthesis flow using the Sky130 standard-cell library. 

# Figure8-Hold Timing Analysis
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/f342c88d-1431-4020-9854-b9843b8ace4c" />


Report Check
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/0e47e9e9-0b9f-4fb7-bb9e-1b7a76a47d5f" />


Report check unconstrained
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/22685345-9617-4484-b42e-b0ef599a027c" />



# Figure9-Setup Timing Analysis for Multi Corners

<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/b4e58691-f647-4e99-9a9f-198b307cd2e1" />
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/247d4dde-b53d-4225-8634-e30ab0b07b32" />
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/85fb68be-ba83-46d7-a7f0-cb5dd282a21f" />


Violated:

<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/281425f0-bc02-4b57-b5dc-14e9ffc05f70" />



 Description:
This screenshot shows the multi-corner timing analysis of the design using the Sky130 standard-cell library. The terminal displays the timing path, including clock arrival time, data required time, and slack values. The reported slack is positive (0.19 ns), indicating that the analyzed timing path meets the required timing constraint for this corner. This analysis helps verify the design’s timing performance under different operating conditions.

# Figure10:Slack Extraction Across 18 Timing Corners
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/637bc36f-9d08-49d6-b8fc-30e1b0009ac3" />

 <img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/d50145ad-d4d2-43df-b397-8b571dbb4fc6" />

The slack values extracted from 18 timing-corner reports using the terminal. 
# Figure11-STA Report Archiving and Plot Generation
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/6592b1d8-f158-4f42-bb8a-97d811e19e1d" />


Description:STA report archiving and plot generation process. The terminal displays the generation and storage of Static Timing Analysis (STA) reports for different timing corners. The generated reports are organized for further analysis and visualization, helping to compare timing results and identify possible timing violations across the design.
# Figure12-Multi-Corner TNS/WNS Report Generation

<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/e5431f55-1d70-4d06-9714-36b5f04ea055" />

 Description:
The Multi-Corner Total Negative Slack (TNS) and Worst Negative Slack (WNS) report generation process. The terminal displays timing results collected for multiple timing corners. These reports are used to evaluate the overall timing performance of the design and identify corners where timing violations occur.

# Figure13- TNS and WNS Value Extraction
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/114da6d7-1ad1-4146-b24f-9c25bc59637d" />


 Description:
The extraction of Total Negative Slack (TNS) values from the generated STA reports. The terminal displays TNS values for different timing corners, including corners with 0.00 ns and corners with negative slack values. These results are used to summarize timing performance and identify corners with timing violations.

# Figure 14 – Completion of WNS/TNS Extraction
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/458c39b8-fb7c-4fa2-a41c-94074b07bf56" />

Description:
completion of WNS/TNS extraction across 18 timing corners. The terminal displays the extracted hold-slack values for each corner and confirms that the timing data has been successfully processed. The results are used to evaluate hold timing performance across different operating conditions.

# Figure 15 – Timing Quality Plot Generation
<img width="2370" height="1275" alt="image" src="https://github.com/user-attachments/assets/2179d19f-4fcc-4228-91a1-80940799247b" />


# Figure 16 – Worst Setup Slack Across 18 SKY130 HD Corners

<img width="2370" height="1275" alt="image" src="https://github.com/user-attachments/assets/dd185dc7-4123-4e61-a530-edf1dfd83aac" />

# Figure 17 – Total Negative Slack Across 18 Corners

<img width="2372" height="1275" alt="image" src="https://github.com/user-attachments/assets/19ec637b-4c9a-4c36-8552-be5ae62d1845" />


# Figure 18– WNS and Minimum Hold Slack Across 18 Corners
<img width="2371" height="1275" alt="image" src="https://github.com/user-attachments/assets/7900b0d2-5251-4e60-af36-2295f8e4f6a3" />

# Results:

The 18-corner Static Timing Analysis (STA) of the VSDBabySoC design was completed successfully.

Number of timing corners: 18

Worst Setup Slack (WNS): −62.6 ns

Total Negative Slack (TNS): −1355.80 ns

Minimum Hold Slack: +1.83 ns

Hold timing: Positive slack was observed across the analyzed corners.

The SS n40C 1.28 V corner shows the most significant setup timing degradation.


These results show that the major timing issue in the analyzed design is related to setup timing, while the hold timing remains positive.

# Conclusion:

The VSDBabySoC design was successfully analyzed using OpenSTA across 18 SKY130 HD timing corners. The analysis produced a worst setup slack of −62.6 ns and a TNS of −1355.80 ns, indicating setup timing violations at selected corners. The minimum hold slack of +1.83 ns indicates positive hold margin in the analyzed results. The extracted WNS, TNS, and hold-slack values provide useful information for further timing optimization and design refinement.


