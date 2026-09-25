# Power Distribution Network (PDN) & Detailed Routing (TritonRoute)

# Aim
To implement the Power Distribution Network (PDN), perform detailed routing using TritonRoute, and complete Design Rule Checking (DRC) layout verification for the picorv32a RISC-V core using the Sky130 PDK in the OpenLANE flow.  
# Objectives
Construct power and ground rings, straps, and rails (gen_pdn) across the layout post Clock Tree Synthesis (CTS).  
Perform global and detailed routing using OpenROAD and TritonRoute without setup or hold timing violations.  
Stream out the GDSII file using Magic and verify the physical design rules to achieve 0 DRC errors.  
Resolve state execution dependencies and disk space constraints encountered during interactive flow runs.  
# Procedure
 Environment Setup & Prerequisites:
Launched the OpenLANE interactive Tcl shell and initialized the target design:
 package require openlane 0.9
prep -design picorv32a -tag RUN_MOD5_CLEAN -overwrite
Sequentially executed prerequisite physical design steps: Synthesis \rightarrow Floorplan \rightarrow Placement \rightarrow Clock Tree Synthesis (CTS).
 Power Distribution Network (PDN) Generation:
 Configured power (VDD) and ground (VSS) net environment variables to prevent Tcl state corruption.
 Executed PDN generation to build power grid rings and standard cell rails:
 set ::env(VDD_NET) "VDD"
set ::env(GND_NET) "VSS"
gen_pdn
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/12e3af38-b4b7-4203-bea3-7dcc21400078" />
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/86393601-2599-45da-8b37-827c87b1f5c7" />

 Detailed Routing:
 Ran global and detailed routing via TritonRoute to route all design signals across Sky130 metal layers:
 run_routing
GDSII Streaming & DRC Verification:
 Cleared legacy run logs and temporary build directories (rm -rf) to address VM disk space limits (0 bytes remaining).
 Streamed out layout data to GDSII format and performed full physical DRC verification using Magic:
 run_magic
run_magic_drc
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/8536be3d-8b58-4756-8613-1206b286a171" />
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/a90eb03a-6826-4667-a7b9-ca0d53ee0554" />
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/7e66377a-7178-4fbf-b815-4af99c37ad9a" />

# Report Generation:
Generated final runtime and overall summary reports:
 calc_total_runtime
generate_final_summary_report
<img width="1280" height="767" alt="image" src="https://github.com/user-attachments/assets/0276d402-3260-433c-ba7f-d62308821f01" />

# Results
PDN Status: Power grid successfully synthesized and connected to standard cell supply pins.  
Routing Status: TritonRoute completed detailed routing with no remaining unrouted nets.  
Layout Rule Checks: Magic DRC verification confirmed 0 DRC violations on the streamed GDSII layout.
Report Artifacts: Final statistics saved under /openLANE_flow/designs/picorv32a/runs/RUN_MOD5_CLEAN/reports/final_summary_report.csv.
# Conclusion
Module 5 of the RTL2GDS flow was successfully completed. The picorv32a design was fully routed, met all layout constraints, and passed complete physical verification with zero DRC violations, producing a clean, tape-out ready GDSII file.
