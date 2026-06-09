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

## Simulation Results & Observations
* Reset Check: When reset is high, counter goes to 0000.
* Counting Check: Counter increments smoothly on every clock edge.

