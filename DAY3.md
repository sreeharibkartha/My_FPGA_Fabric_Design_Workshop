
# Day 3:  RISC-V Processor (Mythcore) on FPGA

## Activity 
The goal for Day 3 was to ditch the simple counter circuit and take on a much heavier, more realistic RTL design. I took a Mythcore RISC-V based processor and pushed it through the entire FPGA flow. 

This lab really helped me see what happens when you throw a massive digital system at the FPGA tools and how it handles the routing, timing, and area.

---

## Where I Got the Source Code
Grabbed the core files for this experiment:
* `mythcore_test.v`
* `mythcore_test_gn.v`

**Shoutout to the reference repo:** [https://github.com/shivanishah269/risc-v-core/tree/master](https://github.com/shivanishah269/risc-v-core/tree/master)

---

## My FPGA Design Flow
Here is the step-by-step pipeline I followed today:
1. RTL Design & Coding
2. Functional Simulation (Just a sanity check before synthesizing)
3. Logic Synthesis
4. Netlist Generation
5. Placement & Routing (The heavy lifting)
6. Timing Analysis (Making sure it actually works at speed)
7. Bitstream Generation
8. Hardware Debugging with ILA (Integrated Logic Analyzer)

---

## RTL Simulation
Before burning anything to the board, I ran a quick simulation to prove the Mythcore logic wasn't completely broken.

### What I Noticed:
* The primary clock toggled exactly as expected.
* Hitting the reset line properly flushed the processor state.
* The output waveforms shifted correctly based on the instructions it was executing. 

### RTL Simulation Waveform

<img width="1167" height="261" alt="Screenshot 2026-06-10 000608" src="https://github.com/user-attachments/assets/b54c1b39-07df-4b54-b084-06c4444190f5" />


---

## Synthesis & RTL Schematic
Vivado crunched the Verilog and spat out the RTL schematic. Since this is a full-blown RISC-V core and not just a counter, the schematic looked like a massive web of logic.

You could clearly spot the heavy datapath, tons of flip-flops, and huge LUT structures handling the decoding. 

### Generated RTL Schematic

<img width="1175" height="372" alt="Screenshot 2026-06-10 000621" src="https://github.com/user-attachments/assets/198faa7c-c3e3-4ad0-899d-6ce38ae4f048" />


---

## Device Package View
Looking at the package view gave me a real sense of how much of the physical silicon this processor ate up. It shows exactly where Vivado decided to drop our logic and how the I/O pins map to the outside world.

### FPGA Package & Resource Map

<img width="497" height="441" alt="Screenshot 2026-06-10 000652" src="https://github.com/user-attachments/assets/33dd3b98-6290-444b-882f-7761d50610cb" />


---

##  ILA (Integrated Logic Analyzer)

### Why I Added It
Looking at blinking LEDs isn't enough for a processor. I instanced an ILA core so I could hook into the internal datapath signals and watch them live in the Vivado Hardware Manager.

###  ILA Instantiation
```verilog
ila_0 your_instance_name (
    .clk(clk),
    .probe0(reset),
    .probe1(out)
);
```

##Signal Probes:
clk: Main system clock
reset: To watch my reset triggers
out: Monitoring the core output bus

##Constraints Used
```
set_property PACKAGE_PIN V6 [get_ports clk]
set_property IOSTANDARD LVCMOS33 [get_ports clk]

set_property IOSTANDARD LVCMOS33 [get_ports reset]
set_property PACKAGE_PIN P2 [get_ports reset]

create_clock -period 10.000 -name clk -waveform {0.000 5.000} [get_ports clk]

set_property C_CLK_INPUT_FREQ_HZ 300000000 [get_debug_cores dbg_hub]
set_property C_ENABLE_CLK_DIVIDER false [get_debug_cores dbg_hub]
set_property C_USER_SCAN_CHAIN 1 [get_debug_cores dbg_hub]

connect_debug_port dbg_hub/clk [get_nets clk_IBUF_BUFG]

```

##Utilization Analysis
The utilization report displayed FPGA resource consumption.

##Resources Used
LUTs
LUTRAM
Flip-Flops
BRAM
IO Blocks

##Utilization Report
<img width="680" height="451" alt="Screenshot 2026-06-10 000721" src="https://github.com/user-attachments/assets/aa7ad352-a903-472e-a753-3c2ba870c57f" />

##Timing Analysis Summary
<img width="1011" height="222" alt="Screenshot 2026-06-10 000738" src="https://github.com/user-attachments/assets/6a96e91d-cc1d-4ade-9920-35fc16cc9bbd" />

##Power Analysis
Power analysis was performed after implementation.

##The report included:

Dynamic power
Static power
Clock power
Logic power
Signal power

##Power Analysis Output
<img width="861" height="357" alt="Screenshot 2026-06-10 000759" src="https://github.com/user-attachments/assets/f7f2f3fa-b828-4203-bc40-65183841a117" />

##Key Takeaways from Today
1. Complexity Scaling is Real
Jumping to a RISC-V core meant routing took way longer, the silicon footprint exploded, and meeting timing got a lot harder.

2. Constraints Make or Break You
If I didn't set up that XDC file correctly, Vivado would have thrown the logic anywhere, missed timing closure, and the ILA wouldn't have synced properly.

##Conclusion
  
Day 3 was a massive success. Going from a simple counter to synthesizing, routing, and debugging a complete Mythcore processor on an FPGA fabric really connected the dots on how industry-level RTL flows work in the real world.



