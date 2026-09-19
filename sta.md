# Static Timing Analysis (STA) for All Corner Libraries
# 1. Objective
The objective of this project is to perform Static Timing Analysis (STA) for the SKY130 HD standard-cell library using different PVT (Process, Voltage and Temperature) corners.
OpenSTA is used to analyse the timing paths and determine whether the design meets the required timing constraints.

# 2. Tools Used
OpenSTA
SKY130 HD Standard Cell Library
Verilog
SDC timing constraints
Ubuntu/Linux

# 3. PVT Corner Definition
The .lib filenames represent different process, temperature and voltage conditions.
Parameter
Meaning
TT
Typical-Typical
FF
Fast-Fast
SS
Slow-Slow
n40C
-40°C
025C
25°C
100C
100°C
1v28
1.28 V
1v60
1.60 V
1v80
1.80 V
1v95
1.95 V
For example:
sky130_fd_sc_hd__tt_025C_1v80.lib
means Typical-Typical process, 25°C temperature and 1.80 V supply voltage.


# 4. STA Flow
The following flow was used for the analysis:
Read Liberty File
       ↓
Read Verilog Design
       ↓
Link Design
       ↓
Read SDC Constraints
       ↓
Run STA
       ↓
Generate Timing Report
The timing report was generated using:
report_checks -path_delay min_max -fields {nets cap slew input_pins fanout}
STA Output Screenshot – Corner 1
The screenshot below shows the OpenSTA timing path, arrival time, required time and slack.
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/65ad8f01-c278-4fcd-8d44-2f0d9522a821" />
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/0284133a-51bd-4185-816b-3837f629113a" />

# 5. STA Results for Different Corners
STA was executed using the available SKY130 HD .lib files. Separate timing reports were generated for the different PVT corners.
The reports were stored in:
~/STA_reports/
Each report contains the timing information for its corresponding corner.
STA Output Screenshot – Corner 2
The following screenshot shows another corner's STA timing report.
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/fa001d49-bfea-4377-84bc-e9a205d8d505" />
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/49c94f44-1074-493a-ba3e-b44202e55331" />

# 6. Timing Path Information
The OpenSTA report provides the following information:
Startpoint
Endpoint
Clock path
Cell delay
Net delay
Capacitance
Slew
Fanout
Data arrival time
Data required time
Slack
STA Output Screenshot – Corner 3
The screenshot shows the detailed timing path and slack reported by OpenSTA.
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/220f132f-e9b2-4080-b535-b504ba9ff3b0" />
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/d2b0f331-f08d-4902-b783-00b829e217c0" />
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/59486599-fbac-429b-9080-4b65f3870c31" />

# 7. Slack Analysis
Slack indicates whether the timing requirement is satisfied.
Slack = Data Required Time - Data Arrival Time
Positive slack → Timing requirement is met.
Negative slack → Timing violation occurs.
Both MET and VIOLATED results are recorded because they represent the actual results obtained at different timing conditions.
STA Output Screenshot – Corner 4
The following screenshot shows the reported slack for another timing corner.
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/520a096c-2280-4e80-af79-77440918424e" />
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/3d32caa5-1ec4-4d02-b226-1b56cba279f2" />
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/741fa912-a0c5-4235-b24c-57fe0981dacd" />

# 8. Generated STA Reports
The generated reports are stored in the STA_reports directory.
Example:
STA_reports/
├── sky130_fd_sc_hd__tt_025C_1v80.txt
├── sky130_fd_sc_hd__ff_025C_1v95.txt
├── sky130_fd_sc_hd__ss_040C_1v40.txt
└── ...
STA Output Screenshot – Corner 5
The screenshot below shows the STA report generated for another corner.
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/b50d0757-dca5-4f15-890b-adf18cc12b0a" />
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/10541c98-f966-471a-913f-055cc74fb05d" />
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/bc031ae7-efcc-45e8-8615-180cce202617" />
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/b31cb4ba-7664-49b1-a98b-7d150c1854b3" />

# 9. Conclusion
Static Timing Analysis was successfully performed using OpenSTA for the available SKY130 HD corner libraries.
The analysis provides timing-path information and slack values under different PVT conditions. The generated reports and screenshots document the STA results for the analysed corners.
Final STA Screenshot
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/3d11a8d3-e5ed-412b-a634-758897a25355" />

