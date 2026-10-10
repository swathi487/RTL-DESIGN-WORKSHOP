# VSDBabySoC: Physical Design and Multi-Corner Timing Analysis with OpenROAD (sky130hd)

Physical design walkthrough of the **vsdbabysoc** design using the **OpenROAD-flow-scripts** flow on the **SkyWater sky130hd** platform: floorplan, placement, clock tree synthesis (CTS), global routing, and post-route static timing analysis (STA) across 16 PVT corners. The work was done in a GitHub Codespace and viewed through the OpenROAD GUI over noVNC.



## Aim
To run the RTL-to-layout physical design stages for the **vsdbabysoc** design on **sky130hd** using OpenROAD, inspect the layout and reports at each stage, and verify post-route setup and hold timing across multiple process-voltage-temperature (PVT) corners.

## Objectives
- Inspect the floorplan (`2_floorplan`), including the `dac` and `pll` macros and the power grid.
- Analyse the endpoint slack distribution using the OpenROAD Charts widget.
- Examine the placed design (`3_place`): the locked `dac` macro, the VDD power net and standard-cell placement.
- Check placement-stage electrical rules (max capacitance, slew, fanout) and hold timing.
- Visualise the clock tree (`4_cts`).
- View the routed design (`5_route`) with global-routing parasitics.
- Run post-route STA for 16 PVT corners and compare worst setup and hold slack.

## Theory

### Physical design flow
| Stage | Purpose |
|---|---|
| **Floorplan** | Defines die/core area, rows, tracks, I/O pins, macro placement and the power distribution network (PDN). |
| **Placement** | Places standard cells legally in rows, minimising wirelength and congestion while respecting timing. |
| **CTS** | Builds a buffered clock network so all sequential cells receive the clock with low skew and acceptable slew. |
| **Routing** | Global routing plans wire paths through routing regions; detailed routing assigns exact tracks and vias on met1 to met5 (li1 for local interconnect). |

### Static Timing Analysis (STA)
- **Setup slack** = data required time − data arrival time. It must be ≥ 0. Setup is most critical at **slow** corners, where cells are slowest.
- **Hold slack** = data arrival time − (capture clock time + library hold time). It must be ≥ 0. Hold is most critical at **fast** corners, where data arrives soonest.
- **Endpoint slack histogram** shows how many timing endpoints fall in each slack interval.
- **Unconstrained pins** have no timing constraint applied in the SDC.

### PVT corners
Cell delay depends on **Process** (ss = slow, tt = typical, ff = fast), **Voltage** and **Temperature**. A design is signed off only when timing is met at the corners it is specified for. Corner names follow `<process>_<temperature>_<voltage>`, for example `ss_n40C_1v28` = slow process, −40 °C, 1.28 V.

### Electrical design rule checks
- **Max slew**: limit on signal transition time at a pin.
- **Max capacitance**: limit on the load a driver can drive.
- **Max fanout**: limit on the number of loads driven by one net.

### Parasitic estimation
- `estimate_parasitics -placement` estimates wire RC from cell placement.
- `estimate_parasitics -global_routing` estimates wire RC from the global routes, which is more accurate after routing.

### Power network
VDD and VSS are routed as special nets on the upper metal layers in a grid, connecting to standard-cell rails on met1. Inspector fields such as *Special = True* and *Wire type = ROUTED* confirm a net is a PDN net.

## Tools and Environment
| Item | Detail |
|---|---|
| Flow | OpenROAD-flow-scripts (`flow/`) |
| Platform | `sky130hd` |
| Design | `vsdbabysoc` (variant `base`) |
| Macros | `avsddac` (instance `dac`), PLL (`pll`) |
| Host | GitHub Codespaces, GUI via noVNC |
| Viewer | OpenROAD GUI (Display Control, Inspector, Timing Report, Charts, Scripting) |
| Post-route STA | Per-corner reports in `~/Desktop/postroute_sta/*.rpt` and `summary.txt` |

## Procedure

1. **Launch the environment.** Open the Codespace, connect through noVNC (`vnc_lite.html`) and open a terminal.
2. **Open the floorplan result in the GUI.**
   ```tcl
   read_db ./results/sky130hd/vsdbabysoc/base/2_floorplan.odb
   ```
   The GUI then reads the constraints and parasitics:
   ```tcl
   read_sdc  .../results/sky130hd/vsdbabysoc/base/2_floorplan.sdc
   source    .../platforms/sky130hd/setRC.tcl
   sta::find_timing
   sta::find_requireds
   gui::select_chart "Endpoint Slack"
   gui::update_timing_report
   ```
3. **Inspect the floorplan.** Toggle layers met1 to met5, locate the `dac` and `pll` macros and the PDN. In the **Charts** tab select *Setup Slack* and click *Update*.
4. **Open the placement result** (`3_place`). It loads `3_place.sdc` and runs `estimate_parasitics -placement`. Select the `dac` instance and the `VDD` net and read the Inspector.
5. **Review placement reports** in the terminal: `max_capacitance_check_*`, `max_slew_violation_count`, `max_fanout_violation_count`, `max_cap_violation_count` and `report_checks -path_delay min`.
6. **Open the CTS result** (`4_cts`) and view the clock-tree heat map with rows, tracks and shape types.
7. **Open the routed result** (`5_route`). The GUI loads `5_route.sdc`, checks `grt::have_routes` and runs `estimate_parasitics -global_routing`.
8. **Run post-route STA for all corners** and collect the worst slack from each report:
   ```bash
   cd ~/Desktop/postroute_sta
   for f in *.rpt; do
     echo "$f: $(grep -m1 'worst slack max' $f) | $(grep -m1 'worst slack min' $f)"
   done
   ```
9. **Plot** worst setup slack (max) and hold slack (min) against PVT corner.

## Results

### 1. Floorplan: endpoint slack and macros

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/7c68f507-b61f-4186-90b0-e6fba03060f8" />

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/3ac33a10-e13b-4748-8e1c-db253d4321f2" />



- The largest bin of the Endpoint Slack histogram holds **118 endpoints in the [5, 6) ns interval**, so most endpoints have comfortable setup margin at this stage.
- **17 unconstrained pins** were reported.


<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/0df4e17f-f0a1-4a19-849b-f8e270cefe67" />


### 2. Placement: macro and power net

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/3676df13-e5cd-4711-ae8f-dc8dc2b15280" />

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/62e46d63-adef-430a-913d-dd6ec50b0334" />


| Property | Value |
|---|---|
| Instance | `dac` |
| Master | `avsddac` (Macro) |
| Placement status | LOCKED |
| Orientation | R0 |
| X | 150.825 µm |

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/c79e1dfb-d8d1-4136-a389-f437a2b203da" />

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/3f04e93b-fd2a-437a-9ff0-fbbdc0995252" />


| Property | Value |
|---|---|
| Net | `VDD` |
| Signal type | POWER |
| Wire type | ROUTED |
| Special | True |
| ITerms | 35710 items |
| BTerms | 1 item |

![Placed cells](images/place_cells_zoom.png)

### 3. Placement: electrical checks


| Check | Result |
|---|---|
| Max capacitance slack | 0.0153 |
| Max capacitance limit | 0.0504 |
| Max capacitance slack limit | 0.3045 |
| Max slew violation count | **0** |
| Max fanout violation count | **0** |
| Max cap violation count | **0** |

### 4. Placement: hold timing

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/3f921943-b13e-4eec-8837-3a5643e87c5a" />


| Item | Value |
|---|---|
| Startpoint | `core.CPU_is_s_instr_a3$_DFF_P_` (clk, rising edge) |
| Endpoint | `core.CPU_is_s_instr_a4$_DFF_P_` (clk, rising edge) |
| Path type | min (hold) |
| Data arrival time | 0.28 ns |
| Library hold time | −0.03 ns |
| **Hold slack** | **0.31 ns (MET)** |

### 5. Clock tree synthesis

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/c10ddc81-b6be-493b-a8d5-34b659fa507f" />

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/07d4a8cb-3db0-4234-b728-144b4170840f" />


The placed cells form a compact cluster inside the die, with the clock-tree heat map overlaid on the power grid.

### 6. Routing


<img width="1600" height="900" alt="image" src="https://github.com/user-attachments/assets/eb2ef7ac-10c5-4972-9d84-a451733d787e" />

The `5_route` view shows the routed design on met1 to met5, with the VDD/VSS grid visible as vertical straps along the right edge. The GUI confirmed routes exist (`grt::have_routes`) and used global-routing parasitics for timing.

### 7. Post-route STA across PVT corners

<img width="1043" height="832" alt="image" src="https://github.com/user-attachments/assets/1cb1f1cb-277f-4a92-8199-778e303c6a3f" />


Worst slack per corner (values read from the plots; see note below the table):

| Corner | Setup slack (ns) | Hold slack (ns) | Setup status |
|---|---|---|---|
| ff_100C_1v65 | 8.15 | 0.25 | Met |
| ff_100C_1v95 | 9.03 | 0.20 | Met |
| ff_n40C_1v56 | 6.91 | 0.29 | Met |
| ff_n40C_1v65 | 7.62 | 0.25 | Met |
| ff_n40C_1v76 | 8.24 | 0.23 | Met |
| ff_n40C_1v95 | 8.94 | 0.19 | Met |
| ss_100C_1v40 | −2.72 | 0.89 | **Violated** |
| ss_100C_1v60 | 2.10 | 0.63 | Met |
| ss_n40C_1v28 | −29.05 | 1.68 | **Violated** |
| ss_n40C_1v35 | −15.57 | 1.25 | **Violated** |
| ss_n40C_1v40 | −9.98 | 1.05 | **Violated** |
| ss_n40C_1v44 | −6.71 | 0.94 | **Violated** |
| ss_n40C_1v60 | 0.43 | 0.63 | Met |
| ss_n40C_1v76 | 3.73 | 0.48 | Met |
| tt_025C_1v80 | 7.13 | 0.31 | Met |
| tt_100C_1v80 | 7.26 | 0.31 | Met |

> **Note:** Corner labels and a few values in the terminal screenshot (for example `ss_n40C_1v40` hold, and the last three rows) differ slightly from the plots. The table follows the plots, which list 16 distinct corners. Re-run the loop above and confirm against `summary.txt` before submitting.



<img width="1600" height="866" alt="image" src="https://github.com/user-attachments/assets/442ddada-cf03-447f-be87-7165933a8128" />

<img width="1600" height="866" alt="image" src="https://github.com/user-attachments/assets/157eb9b0-79f7-4b05-88cc-60191c47fc0c" />


**Observations**
- **Setup:** negative at the slow, low-voltage corners. The worst is **−29.05 ns at `ss_n40C_1v28`**. Setup improves steadily as voltage rises (−29.05 → −15.57 → −9.98 → −6.71 ns for 1.28 → 1.35 → 1.40 → 1.44 V at −40 °C). All fast and typical corners meet setup with several ns of margin.
- **Hold:** positive at every corner. The tightest is **0.19 ns at `ff_n40C_1v95`** (fast, high voltage), as expected for hold.

## Conclusion
- The floorplan, placement, CTS and routing stages of **vsdbabysoc** on **sky130hd** were run and inspected in the OpenROAD GUI.
- Placement showed **no max slew, fanout or capacitance violations**, and the inspected hold path met timing with +0.31 ns slack.
- Post-route STA over 16 PVT corners shows **hold is met at all corners** (minimum 0.19 ns).
- **Setup is met at typical and fast corners but fails at the slow, low-voltage corners** (`ss_100C_1v40` and `ss_n40C_1v28`/`1v35`/`1v40`/`1v44`), with the worst case at −29.05 ns. Setup recovers as supply voltage increases.
- Possible next steps: relax the clock period or target only the corners the design is specified for, run `repair_timing` or cell resizing to fix setup at slow corners, review the 17 unconstrained pins in the SDC, and run DRC/LVS signoff.



