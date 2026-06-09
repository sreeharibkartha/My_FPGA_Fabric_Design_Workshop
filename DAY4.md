# Day 4: Hands-on with the SOFA FPGA Fabric

## Overview
Day 4 was all about ditching standard commercial architectures and moving over to the **SOFA (Skywater Open-source FPGAs) Fabric**. I used the OpenFPGA framework to generate the fabric and then pushed a custom design through it.

The complete flow involved:
* Firing up OpenFPGA
* Generating the raw FPGA fabric
* Running the VTR flow targeting this new architecture
* Placement, routing, and timing analysis
* Power estimation
* Post-synthesis simulation to verify logic

**The Test Subject:**  4-bit Up Counter.

---

## Introduction to SOFA FPGA Fabric
SOFA is a completely open-source FPGA architecture. It’s built on top of the Skywater 130nm PDK and heavily utilizes both the OpenFPGA framework and the VTR flow.

**The specific architecture target I used:** `FPGA1212_QLSOFA_HD_PNR`
* **Max Freq:** 50 MHz
* **Resources:** * 1152 LUTs
  * 2304 Flip-Flops
  * 1152 Soft Adders

---

## OpenFPGA Flow Execution

### Running OpenFPGA
Kicked off the OpenFPGA engine to build the fabric and push the counter design through the custom architecture constraints.

**Command Used:**
```bash
make runOpenFPGA
```
## OpenFPGA Execution Output
Watched the terminal as OpenFPGA spun up its parallel threads and executed the `FPGA1212_QLSOFA_HD_task`. It ran the full Yosys-to-VPR flow and finished cleanly with a return code 0—no fatal crashes! Here is what the console output looked like:

```bash
$ make runOpenFPGA
INFO (    MainThread) - Set up to run 2 Parallel threads
INFO (    MainThread) - Currently running task FPGA1212_QLSOFA_HD_task
INFO (    MainThread) - Removing run_dir run001
INFO (    MainThread) - Removing run_dir latest
INFO (    MainThread) - Task execution completed
INFO (    MainThread) - Set up to run 2 Parallel threads
INFO (    MainThread) - Currently running task FPGA1212_QLSOFA_HD_task
INFO (    MainThread) - Created "run001" directory for current task run
INFO (    MainThread) - Running "yosys_vpr" flow
INFO (    MainThread) - Found 1 Architectures 1 Benchmarks & 1 Script Parameters
INFO (    MainThread) - Created total 1 jobs
INFO (00_up_counter_MIN_ROUTE_CHAN_WIDTH) - 00_up_counter_MIN_ROUTE_CHAN_WIDTH Finished with returncode 0, Time Taken 0:00:05.609496
INFO (00_up_counter_MIN_ROUTE_CHAN_WIDTH) - ***** 0 runs pending *****
INFO (    MainThread) - Task execution completed
```
## Generated Files
```
$cd latest/vpr_arch/up_counter/MIN_ROUTE_CHAN_WIDTH/$ ls
SDC_analysis                  fabric_bitstream.xml            report_timing.setup.rpt                 up_counter.route                 up_counter_run.openfpga        yosys_output.log
SRC                           netlist_renaming.xml            report_unconstrained_timing.hold.rpt    up_counter_ace2_output.txt       up_counter_template.openfpga   yosys_rewrite.log
arch                          openfpgashell.log               report_unconstrained_timing.setup.rpt   up_counter_ace_out.act           up_counter_yosys_out.blif
arch_bitstream.xml            packing_pin_util.rpt            up_counter.blif                         up_counter_ace_out.blif          vpr_stat.result
benchmark                     pre_pack.report_timing.setup.rpt up_counter.net                          up_counter_blif_3args_output.txt vpr_stdout.log
fabric_bitstream.bit          report_timing.hold.rpt          up_counter.place                        up_counter_output_verilog.v      yosys.ys

```

## Counter Design Used
## Counter Verilog Code
```
/*Important: Once you run ./a.out, it will keep running infinitely,
because it is in an always block.
You need to hit Ctrl+Z to stop it,
else, the vcd will become a large file and will never end.
*/

module up_counter (
    out ,      // Output of the counter
    enable ,   // enable for counter
    clk ,      // clock Input
    reset      // reset Input
);

output [3:0] out;

//you can alternately write this as output reg [7:0] out;

input enable, clk, reset;

//----------Internal Variables--------------

reg [3:0] out;

always @(posedge clk)

if (reset) begin
    out = 4'b0 ;
end

else if (enable) begin
    out = out + 1;
end

endmodule
```
## Counter Area Report
<img width="1170" height="532" alt="Screenshot 2026-06-10 003317" src="https://github.com/user-attachments/assets/c250b70f-dd37-49b0-83df-6c1e7cf60697" />

<img width="572" height="542" alt="Screenshot 2026-06-10 003354" src="https://github.com/user-attachments/assets/31acb0fd-c5a5-41fa-a4c9-b3017e14aa8a" />

## Constraint File
The timing constraints were provided using an .sdc file.
## Example Constraint File
```
create_clock -period 10 up_counter_clk

set_input_delay  -clock up_counter_clk -max 0 [get_ports {*}]

set_output_delay -clock up_counter_clk -max 0 [get_ports {*}]
```
## Modifying VPR Command
The VPR command was modified to include timing constraints.

## Updated VPR Command
<img width="1168" height="203" alt="Screenshot 2026-06-10 004528" src="https://github.com/user-attachments/assets/26aeead7-cbc8-409d-8640-fe30d39df031" />

##nPost-Synthesis Simulation
Post-synthesis simulation was performed using the generated netlist and timing files.

## The generated files included:
.blif
.net
.place
.route
.sdf

## Post-synthesis Verilog netlist

## Post-Synthesis Simulation Command
<img width="1167" height="202" alt="Screenshot 2026-06-10 004706" src="https://github.com/user-attachments/assets/99f207f3-b0a1-4b02-905f-0195f54509e4" />

## Post-Synthesis Simulation Output
<img width="1222" height="186" alt="image" src="https://github.com/user-attachments/assets/9c682be6-b21b-489c-a0cd-059747513184" />

Post-synthesis simulation waveform of the up counter.
## Timing Violation Analysis
Initially, setup timing violations were observed during implementation.

After adding proper constraints:
1)Setup timing was met
2)Hold timing was satisfied
3)Positive slack was achieved

## Setup Timing Report
<img width="1163" height="598" alt="Screenshot 2026-06-10 004953" src="https://github.com/user-attachments/assets/bd4827e6-3e5e-4645-b43e-e74193e25b5b" />

Setup timing analysis report after implementation.

## Hold Timing Report
<img width="1055" height="562" alt="Screenshot 2026-06-10 005011" src="https://github.com/user-attachments/assets/a52b76c2-96dc-4d0e-9951-27e3b42f2865" />

Hold timing analysis repot after implementation.

Power Analysis
Power analysis was performed using the generated reports from the VTR flow.

The report included:

1)Dynamic Power
2)Static Power
3)Clock Power
4)Logic Power
5)Signal Power

## Power Report
<img width="1163" height="385" alt="Screenshot 2026-06-10 005026" src="https://github.com/user-attachments/assets/11d6ea81-fd73-4575-8d44-60bbe4595d7b" />


## Important Files Generated
The following important files were generated during the OpenFPGA flow:

## File	Description
counter.blif	BLIF netlist generated after synthesis
counter.net	Netlist connectivity
counter.place	Placement report
counter.route	Routing report
counter.sdf	Timing delay information
counter_post_synthesis.v	Post synthesis Verilog netlist
report_timing.setup.rpt	Setup timing report
report_timing.hold.rpt	Hold timing report
vpr_stdout.log	VPR execution log
power.rpt	Power analysis report

## Key Learnings
Understood SOFA FPGA architecture
Learned OpenFPGA execution flow
Performed FPGA fabric generation
Analyzed timing reports
Observed setup and hold violations
Applied timing constraints using SDC
Performed post-synthesis simulation
Analyzed FPGA power consumption
Explored generated implementation reports
