# Day 2: OpenFPGA and VPR Architecture Analysis

## OpenFPGA
* Open-source framework for building custom FPGA fabrics.
* Generates synthesizable Verilog files from XML layout architectures.
* Supports automated layout generation and configuration bitstream creation.

---

## VTR (Verilog-to-Routing)
* Open-source CAD flow software for FPGA architecture exploration.
* Takes Verilog designs and maps them onto target FPGA fabrics.
* Includes tools for synthesis, technology mapping, placement, and routing.

---

## VPR (Versatile Place and Route)
* Core CAD engine inside the VTR hardware flow.
* Performs physical implementation tasks on target architectures.
* Evaluates exact speed, area efficiency, and power consumption.

---

## VPR Execution Command
`$VTR_ROOT/vpr/vpr \
$VTR_ROOT/vtr_flow/arch/timing/EArch.xml \
<blif-file-path> \
--route_chan_width 100 \
--disp on`

---

## VPR Placement & Routing Visualization
* Visual interface showcasing the physical placement of blocks and routed signals across the fabric grid.

<img width="1158" height="902" alt="Screenshot 2026-06-09 195425" src="https://github.com/user-attachments/assets/4e9c4b85-7639-4b51-93b7-ec5cd52af5a9" />


---

## VPR Architectural Analysis & Execution
* Generates detailed hardware resource reports, timing delay calculations, and channel routing diagnostics.

`$VTR_ROOT/vpr/vpr \
$VTR_ROOT/vtr_flow/arch/timing/EArch.xml \
$VTR_ROOT/vtr_flow/benchmarks/blif/tseng.blif \
--route_chan_width 100 \
--disp on`

---

## FPGA Architecture Visualization
* Grid layout containing logic blocks, I/O pads, and routing tracks.
* Configurable Logic Blocks (CLBs) arranged uniformly in a 2D array.
* Horizontal and vertical routing channels running between logic blocks.
* Programmable switch boxes controlling wire intersections and routing paths.

---

## Nets Analysis
* Tracks the physical routing connection paths of multi-terminal signal networks.
* Measures individual wire segment lengths and fan-out distribution counts.
* Evaluates local vs global wire routing density across the hardware fabric.

<img width="743" height="798" alt="Screenshot 2026-06-09 195552" src="https://github.com/user-attachments/assets/dc30d37f-f1cd-436f-adfb-1e8f807d9065" />




---

## Logical Connections
* Maps the logic ports of internal blocks to specific routing tracks.
* Configures input/output connection blocks for seamless signal transfers.
* Optimizes multiplexer choices inside switch boxes to reduce signal delays.

<img width="742" height="790" alt="Screenshot 2026-06-09 195635" src="https://github.com/user-attachments/assets/ce289bb6-cb45-472f-b84e-47d10c2532ba" />


---

## Critical Path Analysis
* Pinpoints the absolute slowest data delay path through the circuit gates.
* Determines the maximum operational clock frequency (Fmax) for the hardware design.
* Identifies bottleneck lookup tables (LUTs) and long connection wires causing negative slack.

<img width="1011" height="808" alt="Screenshot 2026-06-09 195707" src="https://github.com/user-attachments/assets/3c56b28e-b917-459a-bb75-54ed2861cd23" />


---

## Routing Optimization
* Adjusts wire channel width limits dynamically to prevent layout congestion.
* Prioritizes critical timing paths to run on faster, low-resistance wires.
* Reduces total parasitic capacitance to minimize dynamic chip power consumption.

<img width="456" height="411" alt="Screenshot 2026-06-09 200733" src="https://github.com/user-attachments/assets/53e54848-3a16-4a2d-998b-2a035eb746a2" />

# Timing Analysis using Constraints

Timing constraints were added using an SDC file.

The constraints file defines:
* Clock period
* Input delays
* Output delays

## SDC Constraint File

**Constraint File**
```tcl
create_clock -period 10 -name pclk 
set_input_delay -clock pclk -max 0 [get_ports {*}] 
set_output_delay -clock pclk -max 0 [get_ports {*}]
```

## Running VPR with Constraints

```bash
$VTR_ROOT/vpr/vpr \
  $VTR_ROOT/vtr_flow/arch/timing/EArch.xml \
  $VTR_ROOT/vtr_flow/benchmarks/blif/tseng.blif \
  --route_chan_width 100 \
  --sdc_file tseng.sdc \
  --disp on
```

## Setup Timing Analysis

Setup timing checks whether data reaches destination registers before the active clock edge.

After adding constraints:
* Setup slack improved
* Timing closure was achieved
* Violations were reduced


##Setup Timing Report
<img width="1171" height="563" alt="Screenshot 2026-06-09 231913" src="https://github.com/user-attachments/assets/5a493943-54a5-4036-ba97-eb8e0f11b934" />

##Hold Timing Analysis
<img width="1163" height="190" alt="Screenshot 2026-06-09 231934" src="https://github.com/user-attachments/assets/2c1cda74-d70a-4a98-ade3-6b9646d467d2" />

## VTR (Verilog-To-Routing) Flow
* Complete end-to-end open-source FPGA toolchain.
* **Flow Pipeline:** RTL Elaboration $\rightarrow$ Synthesis $\rightarrow$ Tech Mapping $\rightarrow$ Packing $\rightarrow$ Place $\rightarrow$ Route $\rightarrow$ Timing.
* **Tools Used:** ODIN II (Synthesis), ABC (Logic Optimization), VPR (Place/Route).

### VTR Execution Command
```bash
$VTR_ROOT/vtr_flow/scripts/run_vtr_flow.py \
  counter.v \
  $VTR_ROOT/vtr_flow/arch/timing/EArch.xml \
  -temp_dir . \
  -route_chan_width 100
```

---

## Lab Experiment: Counter Design for VTR Flow
* Simple 4-bit up-counter used to test the full toolchain.
* *Note: Once you run `./a.out`, it will keep running infinitely because it is in an always block. You need to hit Ctrl + Z to stop it, else the VCD will become a large file and will never end.*

```verilog
module up_counter ( out , enable , clk , reset );
  output [3:0] out;
  input enable, clk, reset;
  reg [3:0] out;

  always @(posedge clk) begin
    if (reset) begin
      out = 4'b0 ;
    end else if (enable) begin
      out = out + 1;
    end
  end
endmodule
```

### VTR Flow Outputs Generated
* Generated crucial implementation files: `.net`, `.place`, `.route`, `.blif`.
* Produced final text reports including:
  * Nets
  * Routing utilization
  * Critical paths
  * Logical interconnections
  * Placement and timing summaries
 
## Nets Report

<img width="864" height="661" alt="photo_2026-06-09_23-36-42" src="https://github.com/user-attachments/assets/d54f5ea2-d577-4d83-9436-dd8dcca61ae8" />


## Logical Connections

<img width="866" height="719" alt="222" src="https://github.com/user-attachments/assets/4b83e43e-ff91-4ee0-a29e-349ddbb160a7" />


## Critical Path Analysis
Figuring out the exact bottleneck of the design. This analysis tracks down the absolute longest timing path, which directly dictates our max operating frequency (Fmax) and the worst-case propagation delay.

## Critical Path Report

<img width="873" height="743" alt="3333" src="https://github.com/user-attachments/assets/87b6bb69-94b1-4677-8e3d-531a7c574015" />


## Timing Analysis using Constraints
Added an SDC file to enforce some real-world timing rules. By defining the target clock period and the input/output delays, we can force the routing engine to prioritize the tricky paths.

## Setup Timing Report

<img width="1168" height="580" alt="Screenshot 2026-06-09 233329" src="https://github.com/user-attachments/assets/fc3bed6b-2432-4958-af78-1a49fee45725" />


## Hold Timing Report

<img width="1162" height="673" alt="Screenshot 2026-06-09 233347" src="https://github.com/user-attachments/assets/fbab0bd3-46af-47a6-b78a-0925233a7470" />


## SDC Constraint File
`` create_clock -period 10 up_counter_clk
set_input_delay -clock up_counter_clk -max 0 [get_ports {*}]
set_output_delay -clock up_counter_clk -max 0 [get_ports {*}]  ``


## Running VPR with Constraints
`` $VTR_ROOT/vpr/vpr \
$VTR_ROOT/vtr_flow/arch/timing/EArch.xml \
counter.pre-vpr.blif \
--route_chan_width 100 \
--sdc_file counter.sdc  ``


## Setup Timing Report
Double-checked that the data signals actually arrive at the destination registers before the active clock edge hits. After injecting the constraints, the setup slack looked way better, we finally hit timing closure, and the violations were cleaned up.

<img width="1168" height="757" alt="Screenshot 2026-06-09 233423" src="https://github.com/user-attachments/assets/71741833-482c-4f54-844a-8f9ec8267ac7" />


## Post Synthesis Simulation
Time to verify if the mapped netlist still works logically. Ran this simulation to guarantee that the actual hardware gates behave exactly like the original Verilog RTL.

## Generating Post Synthesis Netlist
`` $VTR_ROOT/vpr/vpr \
$VTR_ROOT/vtr_flow/arch/timing/EArch.xml \
counter.pre-vpr.blif \
--gen_post_synthesis_netlist on  ``


## Testbench for Post Synthesis Simulation

`` timescale 1ns/1ps

module upcounter_testbench();

reg clk, reset, enable;
wire [3:0] out;

up_counter dut(
    .\up_counter^enable (enable),
    .\up_counter^clk (clk),
    .\up_counter^reset (reset),
    .\up_counter^out~0 (out[0]),
    .\up_counter^out~1 (out[1]),
    .\up_counter^out~2 (out[2]),
    .\up_counter^out~3 (out[3])
);

initial $sdf_annotate("up_counter_post_synthesis.sdf", dut);

initial begin

clk=0;
enable=0;
reset=1;

#20;

reset=0;
enable=1;

end

always
#5 clk=~clk;

endmodule  ``

## Post Synthesis Simulation Results
Analyzed the output waveforms to confirm the circuit logic. The signals are toggling correctly and the overall functionality holds up perfectly post-synthesis.

## Post Synthesis Waveform

<img width="1082" height="350" alt="Screenshot 2026-06-09 233507" src="https://github.com/user-attachments/assets/b9bf86a1-5e53-403c-974d-2e6c8e9b540b" />


## Power Analysis using VTR
Ran the power estimation tool to see how much juice this design is going to pull on the chip. It gives a solid breakdown of dynamic switching power, static leakage, and the routing network overhead.

## Power Analysis Command

`` $VTR_ROOT/vtr_flow/scripts/run_vtr_flow.py \
counter.v \
$VTR_ROOT/vtr_flow/arch/timing/k6_frac_N10_mem32K_40nm.xml \
-power \
-temp_dir . \
-route_chan_width 100  ``

## stdout.log report

<img width="1172" height="443" alt="Screenshot 2026-06-09 233534" src="https://github.com/user-attachments/assets/c4535f43-024d-495d-a151-3198e731991a" />


## Power Report

<img width="1166" height="541" alt="Screenshot 2026-06-09 233556" src="https://github.com/user-attachments/assets/fa9b3105-cd63-4fb8-b9fc-ca2cb62df400" />


## VTR Generated Reports
The complete VTR flow spits out a bunch of useful summary files. It automatically generates detailed text reports covering timing margins, placement coordinates, routing congestion, and power consumption.

## Important Commands Used

### Running VTR Flow

`` $VTR_ROOT/vtr_flow/scripts/run_vtr_flow.py \
counter.v \
$VTR_ROOT/vtr_flow/arch/timing/EArch.xml \
-temp_dir . \
-route_chan_width 100  ``

### Running VPR GUI

`` $VTR_ROOT/vpr/vpr \
$VTR_ROOT/vtr_flow/arch/timing/EArch.xml \
counter.pre-vpr.blif \
--route_chan_width 100 \
--disp on  ``

### Running VPR with Constraints

`` $VTR_ROOT/vpr/vpr \
$VTR_ROOT/vtr_flow/arch/timing/EArch.xml \
counter.pre-vpr.blif \
--route_chan_width 100 \
--sdc_file counter.sdc  ``

### Generating Post Synthesis Netlist

`` $VTR_ROOT/vpr/vpr \
$VTR_ROOT/vtr_flow/arch/timing/EArch.xml \
counter.pre-vpr.blif \
--gen_post_synthesis_netlist on  ``

