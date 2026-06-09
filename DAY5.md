# Day 5: Cranking the RVMYTH RISC-V Core on Custom SOFA Fabric

## Overview
Today was the final : taking the RVMYTH RISC-V core, integrating it with our custom SOFA FPGA fabric, and pushing the whole thing through the OpenFPGA and VTR toolchain. The implementation spat out a mountain of timing, utilization, routing, and simulation reports. We analyzed these to prove the processor actually mapped to the silicon correctly.

---

## SOFA RVMYTH Utilization Report
Checked the post-synthesis mapping to see how much of the SOFA architecture this RISC-V core actually ate up. It consumed a massive chunk of LUTs, latches, CLBs, and routing channels compared to our previous labs. This gave a really clear picture of the hardware cost for a full processor core.

### Circuit Statistics
* **Total Blocks:** 5526
* **Inputs:** 2
* **Latches:** 1807
* **Outputs:** 8
* **0-LUTs:** 4
* **4-LUTs:** 3705

### Net Statistics
* **Total Nets:** 5518
* **Average Fanout:** 3.1
* **Maximum Fanout:** 1807
* **Minimum Fanout:** 1.0

### Timing Graph
* **Timing Graph Nodes:** 22705
* **Netlist Clocks:** 1

*Detailed FPGA primitive block usage and logic element statistics for the RVMYTH implementation.*

<img width="912" height="626" alt="Screenshot 2026-06-10 010243" src="https://github.com/user-attachments/assets/8ba68b14-ef40-4e3c-80a6-1888cddafa48" />


---

## Timing Constraints File
Wrote a quick SDC (Synopsys Design Constraints) file to lock down the clock speed and I/O delays. Without this, the router wouldn't know what speeds to target during placement and routing.

### Constraint File Used (`rvmyth.sdc`)
```tcl
create_clock -period 200 clk
set_input_delay -clock clk -max 0 [get_ports {*}]
set_output_delay -clock clk -max 0 [get_ports {*}]
```

The constraint file defines:
**Primary clock period (200)**
**Input delay constraints**
**Output delay constraints**
SDC constraints file used for RVMYTH implementation on the SOFA FPGA fabric.

<img width="1171" height="125" alt="Screenshot 2026-06-10 010256" src="https://github.com/user-attachments/assets/8527efb0-d110-42a7-a3c1-28afac256910" />

## Timing Analysis
## Setup Timing Analysis
Ran the timing engine post-routing. The setup report confirmed the data arrival times easily beat the required limits. We hit positive timing slack across the board!
<img width="816" height="690" alt="Screenshot 2026-06-10 010308" src="https://github.com/user-attachments/assets/d75db5ae-3268-4ab3-b1d5-f84aa95587a3" />

Setup timing analysis report showing positive timing slack for the RVMYTH core.

## Hold Timing Analysis
Verified that the data holds stable long enough after the active clock edge. Hold timing closure was fully achieved.

<img width="872" height="750" alt="Screenshot 2026-06-10 010319" src="https://github.com/user-attachments/assets/ab76cbba-c9f2-481e-86ff-8fbef4fdb0d4" />

Hold timing analysis report showing successful hold timing closure.



## Post-Implementation Simulation
Fired up the simulator one last time using the fully synthesized, placed, and routed netlist.

The waveform proves the processor didn't break during the FPGA mapping. It behaves perfectly under our clock and reset conditions, with clean output transitions and stable processor execution.
<img width="1191" height="317" alt="Screenshot 2026-06-10 010331" src="https://github.com/user-attachments/assets/a5c31df0-3a3c-476e-8fcc-8bbb2104d9e5" />

Post implementation simulation waveform of the RVMYTH core on SOFA FPGA fabric.

## Final Observations
The RVMYTH processor was successfully mapped onto the custom SOFA FPGA fabric.

FPGA resource utilization skyrocketed compared to the simple counter-based designs.

Timing constraints were successfully met with positive setup and hold slack values.

The OpenFPGA and VTR flow successfully generated all required reports and simulation outputs.

Post implementation simulation verified the correctness of the synthesized FPGA netlist.

## Conclusion
Day 5 is a wrap. The RVMYTH RISC-V core is fully implemented on our custom SOFA architecture using a completely open-source framework (OpenFPGA/VTR). The generated utilization reports, timing analysis, and simulation waveforms confirm that the processor operates exactly as intended while satisfying all constraints.

This experiment perfectly demonstrated the complete FPGA implementation flow—starting from raw RTL design all the way to post-implementation verification using an open-source toolchain.
