# Day 1: FPGA Architecture and Basics

## What is an FPGA?
* Reprogrammable hardware chip.
* Parallel processing speed.
* Configurable digital logic.

---

## FPGA vs ASIC

| Feature | FPGA | ASIC |
| :--- | :--- | :--- |
| Flexibility | Fully reprogrammable anytime | Fixed after manufacturing |
| Setup Cost | Zero NRE cost | Massive upfront cost |
| Time to Market | Days to weeks | Months to years |

---

## FPGA Architecture

### 1. CLB (Configurable Logic Blocks)
* Core logic cell.
* Contains LUTs.
* Contains Flip-Flops.

### 2. LUT (Look-Up Table)
* Acts like truth-table.
* Handles combinational logic.
* Uses small RAM.

### 3. Flip-Flop
* Storage element cell.
* Handles sequential logic.
* Syncs with clock.

### 4. Switch Matrix / Interconnects
* Programmable wire channels.
* Connects logic blocks.
* Routes data signals.

### 5. IOB (Input Output Blocks)
* Chip boundary pins.
* Connects external world.
* Controls voltage levels.

---

## Lab Program Flow
1. RTL Design: Writing Verilog code for our counter.
2. Simulation: Checking the waveforms in Vivado.
3. Synthesis: Converting code into logic gates.
4. Implementation: Placing and routing blocks on the FPGA fabric.
5. Bitstream: Generating the final file to program the chip.

---

---

## Lab Experiment: 4-Bit Counter with Clock Divider

### Vivado RTL Code Implementation
```verilog
module counter_clk_div(
    input clk,
    input rst,
    output reg [3:0] counter_out
);

reg div_clk;
reg [25:0] delay_count;

// 1. Clock Divider Logic
always @(posedge clk) begin
    if(rst) begin
        delay_count <= 26'd0;
        div_clk <= 1'b0;
    end
    else begin
        if(delay_count == 26'd212) begin
            delay_count <= 26'd0;
            div_clk <= ~div_clk;
        end
        else begin
            delay_count <= delay_count + 1;
        end
    end
end

// 2. 4-Bit Counter Logic
always @(posedge div_clk) begin
    if(rst)
        counter_out <= 4'b0000;
    else
        counter_out <= counter_out + 1;
end

endmodule



---

## Behavioral Simulation & Verification
* Purpose: Running simulation in Vivado to check our circuit logic before burning it on the chip.
* Key Verifications:
  * Checked if the primary clock is toggling correctly.
  * Verified that the counter register increments on every positive edge.
  * Confirmed that the synchronous reset instantly zeros the output.

### Lab Output Waveform
> 📸 How to add your picture here: Open this file on GitHub, click the Edit (pencil) icon, delete the text on the line below, and drag-and-drop your waveform screenshot right there.

👉 DRAG AND DROP YOUR SIMULATION WAVEFORM SCREENSHOT HERE 👈

---

## RTL Elaboration Stage
* What it does: Converts our raw Verilog text code into an RTL schematic representation.
* Why it matters: It provides a visual circuit diagram to verify if the tool understood our input ports and code structure correctly before synthesis.

---

## Logic Synthesis Flow
* Definition: Translates our RTL schematic into actual hardware primitives specific to the FPGA.
* Target Resources: Maps the logic functions to LUTs, Flip-Flops, and Carry Chains.
* Key Tasks Performed:
  * Carry out logic optimization to remove redundant gates.
  * Generate initial hardware resource estimation.
  * Initiate the timing calculation engine.

---

## Timing Analysis Checks
* Goal: Verifies if electrical data signals arrive at the destination registers within the required clock cycle limits.
* Critical Parameters Checked:
  * Setup Timing: Ensures new data stabilizes before the next active clock edge arrives.
  * Hold Timing: Ensures data remains steady for a minimum duration after the clock edge passes.
  * Slack Calculation: Defined as $\text{Slack} = \text{Required Time} - \text{Arrival Time}$. A positive slack indicates that our design meets timing successfully.

### Timing Summary Snapshot
> 📸 How to add your picture here: Edit this file on GitHub, delete the line below, and drag-and-drop your Vivado Design Timing Summary screenshot.

👉 DRAG AND DROP YOUR TIMING SUMMARY SCREENSHOT HERE 👈

---

## Hardware Device Utilization
* Overview: Vivado generates custom consumption reports showing exactly how much of the physical FPGA chip resources our design is occupying.
* Tracked Resources: Monitors LUTs, Flip-Flops, I/O Pins, and Block RAM (BRAM).

### Utilization Statistics Report
> 📸 How to add your picture here: Edit this file on GitHub, delete the line below, and drag-and-drop your Device Utilization table screenshot.

👉 DRAG AND DROP YOUR UTILIZATION REPORT SCREENSHOT HERE 👈

---

## Power Analysis Estimation
* Objective: Estimates the thermal and power footprint of our circuit layout on the hardware.
* Power Components Tracked:
  * Dynamic power (active switching logic).
  * Static power (leakage current when idle).
  * Dedicated clock network power.
  * Signal line routing power.

### Power Analysis Graph
> 📸 How to add your picture here: Edit this file on GitHub, delete the line below, and drag-and-drop your Power Report screenshot.

👉 DRAG AND DROP YOUR POWER REPORT SCREENSHOT HERE 👈

---

## Virtual Input/Output (VIO) Debugging
* Concept: A Xilinx IP core that allows us to monitor and drive internal FPGA signals in real-time.
* Key Applications:
  * Eliminates the need for physical onboard push-buttons and LEDs during the initial testing phase.
  * Allows us to inject stimulus signals (like force reset) directly from the Vivado Hardware Manager screen.
