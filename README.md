# My_FPGA_Fabric_Design_Workshop

## Introduction
Welcome to my lab log! This repository documents my hands-on journey exploring open-source FPGA CAD flows and custom fabric generation. The goal of this project was to move from understanding basic place-and-route mechanics all the way to mapping a complete RISC-V processor onto a custom-generated open-source FPGA silicon fabric.

## Tools & Technologies Used
* **OpenFPGA:** Framework for generating customizable FPGA architectures and bitstreams.
* **VTR (Verilog-to-Routing):** The core open-source end-to-end FPGA toolchain.
* **VPR (Versatile Place and Route):** Handles the heavy lifting: packing, placement, routing, and timing analysis.
* **ODIN II & ABC:** Used for initial RTL elaboration, synthesis, and logic optimization.
* **Xilinx Vivado:** Used for schematic generation, XDC constraints, and Integrated Logic Analyzer (ILA) hardware debugging.
* **SOFA (Skywater Open-source FPGAs):** Open-source FPGA architecture based on the Skywater 130nm PDK.

---

## Lab Breakdown

### Day 1: Environment Setup & Basics
* Configured the Linux environment.
* Cloned and built the necessary OpenFPGA and VTR repositories.
* Got familiar with the terminal commands, Makefile executions, and directory structures required to run the open-source toolchains.

### Day 2: Exploring OpenFPGA, VPR, and VTR Flow
* Pushed a simple 4-bit up-counter through the standard VTR pipeline.
* Explored the `EArch.xml` target architecture to understand how hardware primitives are defined.
* Fired up the VPR GUI to visually inspect the hardware grid, routing congestion, and physical logic block placement.
* Learned how to apply basic SDC constraints to manipulate setup and hold timing.

### Day 3: Mythcore RISC-V Processor Implementation
* Scaled things up massively by synthesizing a full Mythcore RISC-V processor.
* Used Vivado to generate RTL schematics and view the silicon package layout.
* Injected an ILA (Integrated Logic Analyzer) into the design to monitor internal datapath signals on the hardware in real-time.
* Analyzed the heavy spike in LUT/FF utilization, routing complexity, and power consumption compared to the simple Day 2 counter.

### Day 4: Introduction to SOFA FPGA Fabric
* Ditched standard commercial architectures and generated a custom open-source FPGA fabric using SOFA (Skywater 130nm PDK).
* Ran the OpenFPGA framework to compile the `FPGA1212_QLSOFA_HD_PNR` physical architecture.
* Mapped our 4-bit counter onto this new custom silicon, wrote strict SDC constraints to achieve timing closure, and ran post-synthesis simulations to verify the gate-level delays.

### Day 5: RVMYTH RISC-V Core on Custom SOFA Fabric
* The final.Integrated the full RVMYTH RISC-V core directly with the custom SOFA FPGA fabric.
* Executed the complete OpenFPGA and VTR flow on a heavy processor workload.
* Analyzed deep circuit statistics (LUTs, latches, routing fanouts) and confirmed positive setup/hold timing slack across thousands of nodes.
* Ran a final post-implementation simulation to prove the processor logic survived the physical routing process completely intact.

### Acknowledgement
Kunal Ghosh – VSD Corp Pvt Ltd
VSD Workshop Team 
