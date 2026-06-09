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

