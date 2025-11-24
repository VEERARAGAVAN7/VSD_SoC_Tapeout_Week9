# WEEK 9 - FINAL DOCUMENTATION FOR VSDBABYSOC

## `Introduction to the VSDBabySoC`
VSDBabySoC is a small yet powerful RISCV-based SoC.

The main purpose of designing such a small SoC is to test three open-source IP cores together for the first time and calibrate the analog part of it.

VSDBabySoC contains one RVMYTH microprocessor, an 8x-PLL to generate a stable clock, and a 10-bit DAC to communicate with other analog devices.

What is VSDBabySoC?
VSDBabySoC is a compact SoC featuring:

- RVMYTH: A simple RISC-V-based CPU core.
- PLL: An 8x Phase-Locked Loop for stable clock generation.
- DAC: A 10-bit Digital-to-Analog Converter for interfacing with analog devices.
Its primary purpose is to integrate and test these IPs collaboratively and calibrate the analog part of the SoC.

![VSDBabySoC](Screenshots/BabySoC_block.png)

### What is SoC
- An SoC is a single-die chip that has some different IP cores on it. These IPs could vary from microprocessors (completely digital) to 5G broadband modems (completely analog).

### What is RVMYTH
- The RVMYTH core is a simple RISC V-based CPU designed for educational purposes and small-scale applications. It provides a practical example of a RISC-V processor implementation.

### What is PLL
- Phase-Locked Loop (PLL): A phase-locked loop or PLL is a control system that generates an output signal whose phase is related to the phase of an input signal. PLLs are widely used for synchronization purposes, including clock generation and distribution.

### What is DAC
- Digital-to-Analog Converter (DAC): A DAC is a system that converts a digital signal into an analog signal. DACs are widely used in modern communication systems, enabling the generation of digitally-defined transmission signals.


## 📂 Directory Structure

```
VSDBabySoC/
├── src/
│   ├── include/                # Header files (.vh)
│   │   ├── sandpiper.vh
│   │   ├── sandpiper_gen.vh
│   │   ├── sp_default.vh
│   │   └── sp_verilog.vh
│   ├── module/                 # RTL design files (.v)
│   │   ├── rvmyth.v
│   │   ├── avsddac.v
│   │   ├── avsdpll.v
│   │   ├── vsdbabysoc.v
│   │   └── testbench.v
|   ├── libs/
|   |   ├──avsddac.lib
|   |   ├──avsdpll.lib
|   |   ├──sky130.lib
├── output/          
|   └── synthesis/              # outputs files (.v,.lef,.def,.spef,.odb)
|   └── floorplan/
|   └── placement/
|   └── Clock tree synthesis/
|   └── routing/
└── README.md                   # Documentation
```

--------------------------------------------------------------------------------------------------------------------------------------------------

## `🧪 VSDBabySoC –  Pre-Synthesis Simulation`

### 📖 Overview

- This stage verifies the RTL design functionality before synthesis.
We use Icarus Verilog (iverilog) for simulation and GTKWave for waveform.

- The design under test is VSDBabySoC, which integrates:
1. RISC-V core (rvmyth.v)
2. DAC (avsddac.v)
3. PLL (avsdpll.v)
4. Top-level SoC (vsdbabysoc.v)


### 🛠️ Tools Required

- Icarus Verilog (iverilog) → compile RTL
- vvp → run simulation
- GTKWave → view waveforms

Install them:

```code:
@veeraragav-victus:~/VSD_Soc_TapeOut_Program/week2/VSDBabySoC$ sudo apt install iverilog gtkwave -y
```
### ▶️ Steps to Run Pre-Synthesis Simulation

1. Compile the RTL with Icarus Verilog
```
code:

iverilog -o output/pre_synth_sim/pre_synth_sim.out -DPRE_SYNTH_SIM -I src/include -I src/module src/module/testbench.v
```

![Compilation of Screenshots](Screenshots/compile.png)


2. Run the Simulation
```
code:

./pre_synth_sim.out 
```

![Simulation Run](Screenshots/simulation.png)


3. View the Waveforms in GTKWave
```
code:

 gtkwave pre_synth_sim.vcd
```

![GTKWave Waveform](Screenshots/pre_synth_wf.png)

--------------------------------------------------------------------------------------------------------------------------------------------------

## `🧪 VSDBabySoC – Synthesis Process`

- This document explains the complete RTL-to-Gates synthesis flow of the VSDBabySoC using Yosys.

- Synthesis is the process of converting the behavioral/RTL Verilog description of the SoC into a gate-level netlist using standard cell libraries (SKY130 PDK) and custom IP blocks (DAC and PLL).


### 📌 Prerequisites

- Installed Yosys
- RTL Design files in src/module/
- Header/include files in src/include/
- Libraries (.lib) available in src/lib/:
- --->avsdpll.lib – PLL cell library
- --->avsddac.lib – DAC cell library
- --->sky130_fd_sc_hd__tt_025C_1v80.lib – SKY130 standard cell library

###  🚀 Step-by-Step Synthesis Flow

### 1️⃣ Invoke Yosys

```
bash

yosys
```
- This invokes the Yosys interactive shell where all synthesis commands are executed.


![Invoke_Yosys](Screenshots/invoke_yosys.png)


### 2️⃣ Load Libraries

```
tcl

yosys> read_liberty -lib ~/VSD_Soc_TapeOut_Program/VSDBabySoC/src/lib/avsdpll.lib
yosys> read_liberty -lib ~/VSD_Soc_TapeOut_Program/VSDBabySoC/src/lib/avsddac.lib
yosys> read_liberty -lib ~/VSD_Soc_TapeOut_Program/VSDBabySoC/src/lib/sky130_fd_sc_hd__tt_025C_1v80.lib

```
- read_liberty -lib → Loads timing and functional models of the cells into Yosys.
- PLL and DAC libs → Define external IPs so that Yosys recognizes them.
- SKY130 .lib → Provides the set of standard cells used for mapping (NAND, NOR, FFs, MUX, INV, etc.).


![Read_Lib](Screenshots/read_lib.png)


### 3️⃣ Read RTL Design Files

```
tcl

yosys > read_verilog src/module/vsdbabysoc.v
yosys > read_verilog -I ~/VSD_Soc_TapeOut_Program/week2/VSDBabySoC/src/include/ \
             ~/VSD_Soc_TapeOut_Program/week2/VSDBabySoC/src/module/rvmyth.v
yosys > read_verilog -I ~/VSD_Soc_TapeOut_Program/week2/VSDBabySoC/src/include/ \
             ~/VSD_Soc_TapeOut_Program/week2/VSDBabySoC/src/module/clk_gate.v
```

- vsdbabysoc.v – Top-level SoC wrapper.
- rvmyth.v – CPU core (RISC-V).
- clk_gate.v – Clock gating logic for power saving.
- -I include/ ensures header files like sp_verilog.vh are accessible.

![Read_Designs](Screenshots/read_modules.png)

### 4️⃣ Synthesize to Generic Netlist

```
tcl 
 
yosys> synth -top vsdbabysoc
```

- Converts RTL to a generic gate-level netlist (not mapped to tech cells yet).
- Ensures vsdbabysoc is recognized as the top module.


![Synthesizing](Screenshots/synth.png)

### 5️⃣ Technology Mapping and Optimization

### Dff Mapping 

```
tcl
yosys> dfflibmap -liberty ~/VSD_Soc_TapeOut_Program/VSDBabySoC/src/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
- dfflibmap → Replaces generic flip-flops with SKY130 standard-cell flip-flops.

![Dff_Mapping](Screenshots/dff_map.png)

### Optimization

```
tcl

yosys> opt
```
- opt → Performs logic optimizations (constant propagation, dead code removal).

![Optimization](Screenshots/optimization.png)

### Technology Mapping

```
tcl

yosys> abc -liberty ~/VSD_Soc_TapeOut_Program/VSDBabySoC/src/lib/sky130_fd_sc_hd__tt_025C_1v80.lib \
    -script +strash;scorr;ifraig;retime;{D};strash;dch,-f;map,-M,1,{D}
```
- abc → The heart of synthesis: maps logic to real SKY130 cells, performs retiming & optimization.

![Lib_Mapping](Screenshots/tech_map.png)

### flatten the Design

```
tcl 

yosys> flatten
```
- flatten → Flattens hierarchy → single-level netlist for easier P&R.

![Flatten](Screenshots/flatten.png)

### Unused cells, wires removal

```
tecl

yosys> setundef -zero
yosys> clean -purge
yosys> rename -enumerate
```
- setundef -zero → Replaces all uninitialized signals with logic 0.
- clean -purge → Removes unused signals/cells.
- rename -enumerate → Renames all nets/cells sequentially for readability.

![Removal](Screenshots/remove_unwants.png)

### 6️⃣ Generate Statistics

```
tcl

yosys> stat
```

- Reports number of cells, type of cells, and area estimate.
- Important for analyzing design size and complexity.


![Statistics](Screenshots/statistics.png)


### 7️⃣ Write Final Netlist

```
tcl

yosys> write_verilog -noattr ~/VSD_Soc_TapeOut_Program/week2/VSDBabySoC/output/post_synth_sim/vsdbabysoc_net.vs
```

- produces the gate-level Verilog netlist.
- Attributes (-noattr) are removed for clean output.
- Netlist is ready for post-synthesis simulation and physical design flow.


![Write_Netlist](Screenshots/write_netlist.png)

--------------------------------------------------------------------------------------------------------------------------------------------------

## `🧪 VSDBabySoC – Post-Synthesis Simulation (GLS)`

After synthesis, the RTL design is converted into a gate-level netlist mapped to the SKY130 standard cell library. To ensure that the synthesized design is functionally equivalent to the RTL, we run a Post-Synthesis Simulation (GLS).


### 📌 Objective

- Verify functionality of synthesized netlist (vsdbabysoc_net.vs).
- Ensure no mismatches exist between RTL simulation and GLS simulation.
- Check for issues like uninitialized signals (X propagation), timing violations, or library mismatches.



### 📂 Files Required

Before running the iverilog command, copy the necessary standard cell and primitive models: 
These files must be present in the same directory as the testbench (src/module) to resolve all module references during compilation.

```
bash

cp -r ~/VSD/sky130RTLDesignAndSynthesisWorkshop/my_lib/verilog_model/sky130_fd_sc_hd.v .
cp -r ~/VSD/sky130RTLDesignAndSynthesisWorkshop/my_lib/verilog_model/primitives.v .

```

To ensure that the synthesized Verilog file (vsdbabysoc_net.v) is available in the src/module directory for further processing or 
simulation, you can copy it from the output directory to the src/module directory. Here is the step to do that:

```
bash

cp -r ~/VSD_Soc_TapeOut_Program/week2/VSDBabySoC/output/post_synth_sim/vsdbabysoc.synth.v ~/VLSI/VSDBabySoC/src/module/
```

### 🚀 Compilation and Simulation

### 1️⃣ Compile Netlist + Testbench + Libraries

```
bash

iverilog -o /home/veeraragavan/VSD_Soc_TapeOut_Program/week2/VSDBabySoC/output/post_synth_sim/post_synth_sim.out -DPOST_SYNTH_SIM -DFUNCTIONAL -DUNIT_DELAY=#1 
	 -I /home/veeraragavan/VSD_Soc_TapeOut_Program/week2/VSDBabySoC/src/include 
	 -I /home/veeraragavan/VSD_Soc_TapeOut_Program/week2/VSDBabySoC/src/module
	    /home/veeraragavan/VSD_Soc_TapeOut_Program/week2/VSDBabySoC/src/module/testbench.v
```
Explanation:
- -I src/include → Ensures header files are included.
- testbench.v → Provides stimulus.
- vsdbabysoc_net.vs → Synthesized design (gate-level).
- sky130_fd_sc_hd.v → Standard cell Verilog models (required for simulation).


![Compililation](Screenshots/compile.png)


### 2️⃣ Run Simulation

```
bash 

output/post_synth_sim/
./post_synth_sim.out
```
- Executes the compiled netlist with testbench.
- Generates a .vcd waveform file for analysis.

![Run_Sim](Screenshots/run_sim.png)

### 3️⃣ View Waveforms in GTKWave

```
bash 
gtkwave post_synth_sim.vcd
```
- Compare outputs of Pre-Synth Simulation vs Post-Synth Simulation.
- Ensure waveforms match exactly (ignoring inertial delays).


### Pre-synth Simulation Waveform

![Waveform](Screenshots/pre_synth_wf.png)


### Post-synth Simulation Waveform

![Waveform](Screenshots/post_synth_wf.png)


### ⚠️ Common Issues in GLS

- Uninitialized signals (X propagation): Often due to flip-flops without reset.
- Mismatch with RTL: May occur if synthesis optimizations removed unused logic.
- Missing standard cell models: Ensure sky130_fd_sc_hd.v is always included.

--------------------------------------------------------------------------------------------------------------------------------------------------
## `🧪 VSDBabySoC –  ⏱️ Static Timing Analysis (STA)`

**Static Timing Analysis (STA)** is a **cornerstone step in the VLSI design flow** — performed after synthesis and before layout (and repeated after layout).  
It verifies that the **design meets timing constraints** without running functional simulations.

STA ensures that signals travel through the circuit within the required time limits to guarantee **reliable operation at the target clock frequency**.

#### STA can be done in various stages of RTL to GDS flow

![STA_FLOW](Screenshots/flow.png)
---

### ⚙️ **Understanding Timing Paths**

Every timing path connects two sequential elements (or I/O ports). A typical **timing path** includes:

Clock Source → Clock Network → Launch Flip-Flop → Combinational Logic → Capture Flip-Flop



**Major timing paths:**
1. **Input to Register Path** – from an input port to a flip-flop.  
2. **Register to Register Path** – between two flip-flops (most common).  
3. **Register to Output Path** – from a flip-flop to an output port.  
4. **Input to Output Path** – purely combinational paths.  

![Timing paths](Screenshots/tp.png)

Each of these paths has **delay** that must satisfy timing constraints.

---

### 🕒 **Arrival Time & Required Time**

### 🟦 **Arrival Time (AT):**
The **actual time** at which a signal arrives at the destination (e.g., D-pin of a flip-flop).  
It includes delays from:
- Source clock edge
- Propagation delay through launch flop
- Delay through combinational logic and interconnect


### 🟨 **Required Time (RT):**
The **latest time** by which the signal must arrive to meet setup or hold constraints.  
It depends on:
- Clock period
- Setup or hold time of the capturing flip-flop
- Clock skew and jitter

---

### ⚖️ **Setup and Hold Analysis**

Timing analysis focuses on ensuring that **setup and hold constraints** are satisfied for every path.

### 🧩 **Setup Analysis:**
Setup analysis ensures that data **arrives before the active clock edge** at the capturing flip-flop.

{Setup Slack} = {Required Time} - {Arrival Time}


- If **Slack ≥ 0** → Setup check passes ✅  
- If **Slack < 0** → Setup violation ❌ (data arrives late)

**Setup checks** are performed at **slow corner (max delay)** because longer delays increase the chance of violations.

### ⚡ **Hold Analysis:**
Hold analysis ensures that data **does not change too soon** after the clock edge, maintaining stability.

{Hold Slack} = {Arrival Time} - {Required Time}


- If **Slack ≥ 0** → Hold check passes ✅  
- If **Slack < 0** → Hold violation ❌ (data changes too early)

**Hold checks** are performed at **fast corner (min delay)** because faster paths may violate the hold constraint.

---

### 🧮 **Types of Timing Checks**

| Type | Description | Example |
|------|--------------|----------|
| **Setup Check** | Ensures data arrives early enough before the next clock edge | Violated if path is too slow |
| **Hold Check** | Ensures data remains stable after the clock edge | Violated if path is too fast |
| **Recovery Check** | Ensures asynchronous control (like reset) deassertion occurs in time before active clock edge | Async reset release |
| **Removal Check** | Ensures async signal is not removed too early after clock edge | Async reset hold time |
| **Pulse Width Check** | Ensures clock or control pulses meet min high/low time | Clock duty cycle check |

---

### 📂 **Files Used in Static Timing Analysis**

| File Type | Extension | Description |
|------------|------------|--------------|
| **Gate-Level Netlist** | `.v` | Output of synthesis (contains cell instances and interconnections) |
| **Standard Cell Library** | `.lib` | Provides timing and power info for each standard cell at specific PVT corner |
| **Constraints File** | `.sdc` | Defines clocks, input/output delays, exceptions, and design constraints |
| **Parasitic File (optional)** | `.spef` / `.sdf` | Captures interconnect delays and capacitances after layout |
| **Timing Report** | `.rpt` | Generated output showing slack, delay, and violations |

---

### 🧠 **STA Flow Summary**

1. Read Libraries
2. Read Netlist
3. Read Contraints
4. Update Timing
5. Report Timing

### Timing Analysis Using Inline Commands

```tcl
sta
```

```tcl
# Load Liberty Libraries (standard cell + IPs)
read_liberty -min /home/veeraragavan/VSD_Soc_TapeOut_Program/week3/STA_analysis/timing_libs/sky130_fd_sc_hd__tt_025C_1v80.lib
read_liberty -max /home/veeraragavan/VSD_Soc_TapeOut_Program/week3/STA_analysis/timing_libs/sky130_fd_sc_hd__tt_025C_1v80.lib

read_liberty -min /home/veeraragavan/VSD_Soc_TapeOut_Program/week3/STA_analysis/timing_libs/avsdpll.lib
read_liberty -max /home/veeraragavan/VSD_Soc_TapeOut_Program/week3/STA_analysis/timing_libs/avsdpll.lib

read_liberty -min /home/veeraragavan/VSD_Soc_TapeOut_Program/week3/STA_analysis/timing_libs/avsddac.lib
read_liberty -max /home/veeraragavan/VSD_Soc_TapeOut_Program/week3/STA_analysis/timing_libs/avsddac.lib

# Read Synthesized Netlist
read_verilog /home/veeraragavan/VSD_Soc_TapeOut_Program/week3/STA_analysis/BabySoC/vsdbabysoc_net.v

# Link the Top-Level Design
link_design vsdbabysoc

# Apply SDC Constraints
read_sdc /home/veeraragavan/VSD_Soc_TapeOut_Program/week3/STA_analysis/BabySoC/vsdbabysoc_synthesis.sdc

# Generate Timing Report 
report_checks -path_delay min_max -fields {nets cap slew input_pins fanout} -digits {4} > timing_report.txt
report_tns -digits {4} > tns_report.txt
report_wns -digits {4} > wns_report.txt

# Optional: also print summary to console
puts "✅ STA completed successfully."

```

![STA_Setup](Screenshots/sta_bsyn.png)


### `Contents of STA Report`

<details> <summary><strong>STA.rpt</strong></summary>

```
Warning: SoC_Sta.tcl line 21, unknown field nets.
Startpoint: _9493_ (rising edge-triggered flip-flop clocked by clk)
Endpoint: _10335_ (rising edge-triggered flip-flop clocked by clk)
Path Group: clk
Path Type: min

Fanout       Cap      Slew     Delay      Time   Description
-------------------------------------------------------------------------------------
                    0.0000    0.0000    0.0000   clock clk (rise edge)
                              0.0000    0.0000   clock network delay (ideal)
                    0.0000    0.0000    0.0000 ^ _9493_/CLK (sky130_fd_sc_hd__dfxtp_1)
     1    0.0017    0.0329    0.2749    0.2749 ^ _9493_/Q (sky130_fd_sc_hd__dfxtp_1)
                    0.0329    0.0000    0.2749 ^ _10335_/D (sky130_fd_sc_hd__dfxtp_1)
                                        0.2749   data arrival time

                    0.0000    0.0000    0.0000   clock clk (rise edge)
                              0.0000    0.0000   clock network delay (ideal)
                              0.0000    0.0000   clock reconvergence pessimism
                                        0.0000 ^ _10335_/CLK (sky130_fd_sc_hd__dfxtp_1)
                             -0.0346   -0.0346   library hold time
                                       -0.0346   data required time
-------------------------------------------------------------------------------------
                                       -0.0346   data required time
                                       -0.2749   data arrival time
-------------------------------------------------------------------------------------
                                        0.3096   slack (MET)


Startpoint: _10450_ (rising edge-triggered flip-flop clocked by clk)
Endpoint: _10015_ (rising edge-triggered flip-flop clocked by clk)
Path Group: clk
Path Type: max

Fanout       Cap      Slew     Delay      Time   Description
-------------------------------------------------------------------------------------
                    0.0000    0.0000    0.0000   clock clk (rise edge)
                              0.0000    0.0000   clock network delay (ideal)
                    0.0000    0.0000    0.0000 ^ _10450_/CLK (sky130_fd_sc_hd__dfxtp_1)
   242    0.6005    5.5249    4.1333    4.1333 ^ _10450_/Q (sky130_fd_sc_hd__dfxtp_1)
                    5.5249    0.0000    4.1333 ^ _8121_/A (sky130_fd_sc_hd__clkinv_1)
   271    0.4510    2.0357    5.0555    9.1889 v _8121_/Y (sky130_fd_sc_hd__clkinv_1)
                    2.0357    0.0000    9.1889 v _8599_/B1 (sky130_fd_sc_hd__o211ai_1)
     1    0.0017    0.3801    0.5665    9.7554 ^ _8599_/Y (sky130_fd_sc_hd__o211ai_1)
                    0.3801    0.0000    9.7554 ^ _10015_/D (sky130_fd_sc_hd__dfxtp_1)
                                        9.7554   data arrival time

                    0.0000   11.0000   11.0000   clock clk (rise edge)
                              0.0000   11.0000   clock network delay (ideal)
                              0.0000   11.0000   clock reconvergence pessimism
                                       11.0000 ^ _10015_/CLK (sky130_fd_sc_hd__dfxtp_1)
                             -0.1386   10.8614   library setup time
                                       10.8614   data required time
-------------------------------------------------------------------------------------
                                       10.8614   data required time
                                       -9.7554   data arrival time
-------------------------------------------------------------------------------------
                                        1.1060   slack (MET)



```
</details>


--------------------------------------------------------------------------------------------------------------------------------------------------
## `🧪 VSDBabySoC – Synthesis, Floorplan and Placement of VSDBabySoC in OpenROAD` 

### `OpenROAD Installation`

#### Steps to install the OpenROAD:
```
#1. Download repository
$ git clone --recursive https://github.com/The-OpenROAD-Project/OpenROAD.git
$ cd OpenROAD

#2. Install dependencies
$ sudo ./etc/DependencyInstaller.sh

#3. Build
$ mkdir build
$ cd build
$ cmake
$ make
$ sudo make install

#4. Run tool
$ openroad
```

The below picture ensures the OpenRoad installation :

![OpenROAD_Installation](Screenshots/openroad.png)

###  RTL2GDS Flow for VSDBabySoC: Initial Steps

1. **Create Directories:**
   - Inside `OpenROAD-flow-scripts/flow/designs/sky130hd/`, create a folder named `vsdbabysoc`.
   - Create another folder named `vsdbabysoc` in `OpenROAD-flow-scripts/flow/designs/src/` and place all Verilog files here.

2. **Copy Folders:**
   - From your `VSDBabySoC` folder, copy the following folders into `sky130hd/vsdbabysoc`:
     - **gds:** Contains `avsddac.gds`, `avsdpll.gds`.
     - **include:** Contains `sandpiper.vh`, `sandpiper_gen.vh`, `sp_default.vh`, `sp_verilog.vh`.
     - **lef:** Contains `avsddac.lef`, `avsdpll.lef`.
     - **lib:** Contains `avsddac.lib`, `avsdpll.lib`.

3. **Copy Constraint and Configuration Files:**
   - Copy `vsdbabysoc_synthesis.sdc` into `sky130hd/vsdbabysoc`.
   - Copy `macro.cfg` and `pin_order.cfg` into `sky130hd/vsdbabysoc`.

4. **Create Config File:**
   - Create a `config.mk` file in `sky130hd/vsdbabysoc` with the required configuration details. 

### `Contents of Config.mk`

<details> <summary><strong>config.mk</strong></summary>

```
    # Design and Platform Configuration
   export DESIGN_NICKNAME = vsdbabysoc
   export DESIGN_NAME = vsdbabysoc
   export PLATFORM    = sky130hd

  # Design Paths
  export vsdbabysoc_DIR = /home/veeraragavan/OpenROAD-flow-scripts/flow/designs/sky130hd/$(DESIGN_NICKNAME)

  # Explicitly list Verilog files for synthesis
   export VERILOG_FILES = /home/veeraragavan/OpenROAD-flow-scripts/flow/designs/src/vsdbabysoc/vsdbabysoc.v \
                         /home/veeraragavan/OpenROAD-flow-scripts/flow/designs/src/vsdbabysoc/rvmyth.v \
                         /home/veeraragavan/OpenROAD-flow-scripts/flow/designs/src/vsdbabysoc/clk_gate.v


  # Include Directory for Verilog Header Files
   export VERILOG_INCLUDE_DIRS = $(vsdbabysoc_DIR)/include

  # Constraints File
    export SDC_FILE = $(vsdbabysoc_DIR)/vsdbabysoc_synthesis.sdc

  # Additional GDS Files
    export ADDITIONAL_GDS = $(vsdbabysoc_DIR)/gds/avsddac.gds \
                            $(vsdbabysoc_DIR)/gds/avsdpll.gds

  # Additional LEF Files
   export ADDITIONAL_LEFS = $(vsdbabysoc_DIR)/lef/avsddac.lef \
                            $(vsdbabysoc_DIR)/lef/avsdpll.lef

  # Additional LIB Files
   export ADDITIONAL_LIBS = $(vsdbabysoc_DIR)/lib/avsddac.lib \
                            $(vsdbabysoc_DIR)/lib/avsdpll.lib

 # Pin Order and Macro Placement Configurations
 export FP_PIN_ORDER_CFG = $(vsdbabysoc_DIR)/pin_order.cfg
 export MACRO_PLACEMENT_CFG = $(vsdbabysoc_DIR)/macro.cfg
 #ADDED BY VEERA
 #export PRE_GLOBAL_ROUTE_TCL = $(vsdbabysoc_DIR)/pre_gr.tcl

 # Clock Configuration
 export CLOCK_PORT = CLK
 export CLOCK_NET  = $(CLOCK_PORT)
 export CLOCK_PERIOD = 11.00

# Floorplanning Configuration
 export DIE_AREA   = 0 0 1600 1600
 export CORE_AREA  = 20 20 1590 1590 
  
  
# Core Area Usage and How close the cells can be placed
 export PLACE_DENSITY = 0.6


# Placement Configuration
export PLACE_PINS_ARGS = -exclude left:0-600 -exclude left:1000-1600 -exclude right:* -exclude top:* -exclude bottom:*

# Tuning for Timing and Buffers
  export TNS_END_PERCENT     = 100
  export REMOVE_ABC_BUFFERS  = 1
  export CTS_BUF_DISTANCE    = 600
  export SKIP_GATE_CLONING   = 1

 # Magic Tool Configuration
   export MAGIC_ZEROIZE_ORIGIN = 0
   export MAGIC_EXT_USE_GDS    = 1
   
   
#ADDE BY VEERA
# Macro placement & channel
export MACRO_PLACE_HALO    = 30 30
export MACRO_PLACE_CHANNEL = 40 40
export MACRO_BLOCKAGE_HALO = 0
export DISABLE_MACRO_PLACEMENT = 1

```
</details>


This script sets up environment variables and configurations for the design and synthesis of a System-on-Chip (SoC) using the OpenROAD flow. The design is based on the "vsdbabysoc" and targets the "sky130hd" platform.

--------

### Key Components of config.mk

#### Design and Platform Configuration
- **DESIGN_NICKNAME & DESIGN_NAME**: Both are set to "vsdbabysoc," serving as the identifier for the design project.
- **PLATFORM**: Specifies the technology platform as "sky130hd," indicating the process node and design rules to be used.

#### Design Paths
- **vsdbabysoc_DIR**: Defines the directory path for the design files as `/home/veeraragavan/OpenROAD-flow-scripts/flow/designs/sky130hd/vsdbabysoc`. This path is constructed using the DESIGN_NICKNAME variable, ensuring consistency and easy access to design resources.

#### Verilog Files for Synthesis
- **VERILOG_FILES**: Lists the Verilog source files required for synthesis:
  - `/home/veeraragavan/OpenROAD-flow-scripts/flow/designs/src/vsdbabysoc/vsdbabysoc.v`: The main Verilog file for the SoC design.
  - `/home/veeraragavan/OpenROAD-flow-scripts/flow/designs/src/vsdbabysoc/rvmyth.v`: A module within the design, possibly a RISC-V core or related component.
  - `/home/veeraragavan/OpenROAD-flow-scripts/flow/designs/src/vsdbabysoc/clk_gate.v`: A module for clock gating, used to manage power consumption by controlling clock signals.

#### Verilog Header Files
- **VERILOG_INCLUDE_DIRS**: Specifies the directory for Verilog header files as `/home/veeraragavan/OpenROAD-flow-scripts/flow/designs/sky130hd/vsdbabysoc/include`.

#### Constraints and Additional Files
- **SDC_FILE**: Points to the constraints file for synthesis located at `/home/veeraragavan/OpenROAD-flow-scripts/flow/designs/sky130hd/vsdbabysoc/vsdbabysoc_synthesis.sdc`.
- **ADDITIONAL_GDS**: Lists additional GDS files required for the design:
  - `/home/veeraragavan/OpenROAD-flow-scripts/flow/designs/sky130hd/vsdbabysoc/gds/avsddac.gds`
  - `/home/veeraragavan/OpenROAD-flow-scripts/flow/designs/sky130hd/vsdbabysoc/gds/avsdpll.gds`
- **ADDITIONAL_LEFS**: Lists additional LEF files:
  - `/home/veeraragavan/OpenROAD-flow-scripts/flow/designs/sky130hd/vsdbabysoc/lef/avsddac.lef`
  - `/home/veeraragavan/OpenROAD-flow-scripts/flow/designs/sky130hd/vsdbabysoc/lef/avsdpll.lef`
- **ADDITIONAL_LIBS**: Lists additional LIB files:
  - `/home/veeraragavan/OpenROAD-flow-scripts/flow/designs/sky130hd/vsdbabysoc/lib/avsddac.lib`
  - `/home/veeraragavan/OpenROAD-flow-scripts/flow/designs/sky130hd/vsdbabysoc/lib/avsdpll.lib`

#### Pin Order and Macro Placement
- **FP_PIN_ORDER_CFG**: Configuration file for pin order located at `/home/veeraragavan/OpenROAD-flow-scripts/flow/designs/sky130hd/vsdbabysoc/pin_order.cfg`.
- **MACRO_PLACEMENT_CFG**: Configuration file for macro placement located at `/home/veeraragavan/OpenROAD-flow-scripts/flow/designs/sky130hd/vsdbabysoc/macro.cfg`.

#### Clock Configuration
- **CLOCK_PORT & CLOCK_NET**: Defines the clock port and net as `CLK`.
- **CLOCK_PERIOD**: Sets the clock period to `20.0` units.

#### Floorplanning Configuration
- **DIE_AREA**: Specifies the die area dimensions as `0 0 1600 1600`.
- **CORE_AREA**: Specifies the core area dimensions as `20 20 1590 1590`.

#### Placement Configuration
- **PLACE_PINS_ARGS**: Arguments for pin placement, excluding certain areas on the die:
  - `-exclude left:0-600`
  - `-exclude left:1000-1600`
  - `-exclude right:*`
  - `-exclude top:*`
  - `-exclude bottom:*`

#### Timing and Buffer Tuning
- **TNS_END_PERCENT**: Sets the target negative slack end percentage to `100`.
- **REMOVE_ABC_BUFFERS**: Enables removal of ABC buffers, set to `1`.
- **CTS_BUF_DISTANCE**: Sets the buffer distance for clock tree synthesis to `600`.
- **SKIP_GATE_CLONING**: Skips gate cloning during synthesis, set to `1`.

#### Magic Tool Configuration
- **MAGIC_ZEROIZE_ORIGIN**: Configuration for zeroizing the origin, set to `0`.
- **MAGIC_EXT_USE_GDS**: Configuration for using GDS files, set to `1`.

This setup script is crucial for defining the environment and parameters needed for successful synthesis and layout of the "vsdbabysoc" design on the "sky130hd" platform, ensuring that all necessary files and configurations are in place for the design flow.

### `File Structure After Setup`

```shell
veeraragavan@veeraragavan-victus:~/OpenROAD-flow-scripts/flow$ ls -ltrh designs/src/vsdbabysoc/
total 3.1M
-rw-rw-r-- 1 veeraragavan veeraragavan 1.1K Jun 29 15:50 avsddac.v
-rw-rw-r-- 1 veeraragavan veeraragavan  947 Jun 29 15:50 avsdpll.v
lrwxrwxrwx 1 veeraragavan veeraragavan   87 Jun 29 15:50 primitives.v -> /home/veeraragavan/VLSI/sky130RTLDesignAndSynthesisWorkshop/my_lib/verilog_model/primitives.v
-rw-rw-r-- 1 veeraragavan veeraragavan 1.7K Jun 29 15:50 clk_gate.v
-rw-rw-r-- 1 veeraragavan veeraragavan  17K Jun 29 15:50 rvmyth.v
-rw-rw-r-- 1 veeraragavan veeraragavan  19K Jun 29 15:50 rvmyth_gen.v
-rw-rw-r-- 1 veeraragavan veeraragavan 2.3M Jun 29 15:50 sky130_fd_sc_hd.v
-rwxrwxr-x 1 veeraragavan veeraragavan 1.3K Jun 29 15:50 testbench.v
-rw-rw-r-- 1 veeraragavan veeraragavan  603 Jun 29 15:50 testbench.rvmyth.post-routing.v
-rw-rw-r-- 1 veeraragavan veeraragavan  590 Jun 29 15:50 vsdbabysoc.v
-rw-rw-r-- 1 veeraragavan veeraragavan 743K Jun 29 15:50 vsdbabysoc.synth.v
```

```shell
veeraragavan@veeraragavan-victus:~/OpenROAD-flow-scripts/flow$ ls -ltrh designs/sky130hd/vsdbabysoc/
total 32K
drwxrwxr-x 2 veeraragavan veeraragavan 4.0K Jun 29 15:52 gds
drwxrwxr-x 2 veeraragavan veeraragavan 4.0K Jun 29 15:52 include
drwxrwxr-x 2 veeraragavan veeraragavan 4.0K Jun 29 15:53 lef
-rw-rw-r-- 1 veeraragavan veeraragavan   73 Jun 29 15:54 vsdbabysoc_synthesis.sdc
lrwxrwxrwx 1 veeraragavan veeraragavan   65 Jun 29 15:55 macro.cfg -> /home/veeraragavan/VLSI/VSDBabySoC/src/layout_conf/vsdbabysoc/macro.cfg
lrwxrwxrwx 1 veeraragavan veeraragavan   69 Jun 29 15:55 pin_order.cfg -> /home/veeraragavan/VLSI/VSDBabySoC/src/layout_conf/vsdbabysoc/pin_order.cfg
-rw-rw-r-- 1 veeraragavan veeraragavan 2.1K Jun 29 15:59 config.mk
drwxrwxr-x 2 veeraragavan veeraragavan 4.0K Jun 29 16:06 lib
```


## `🧪 VSDBabySoC —  Synthesis`

Before running the updated flow, make sure to remove any previously generated results, logs, and intermediate files. Use the following command:

```shell

cd OpenROAD-flow-scripts/flow
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk clean_all
```

```shell
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk synth
```

![Alt Text](Screenshots/synth1.png)

**Synthesis Stats**

```shell
gvim reports/sky130hd/vsdbabysoc/base/synth_stat.txt
```

![Alt Text](Screenshots/synth2.png)


**Synthesis netlist**

```shell
gvim results/sky130hd/vsdbabysoc/base/1_2_yosys.v
```
![Alt Text](Screenshots/synth3.png)

**Synthesis Check**

```shell
gvim reports/sky130hd/vsdbabysoc/base/synth_check.txt
```

![Alt Text](Screenshots/synth4.png)


## `VSDBabySoC — Floorplan`

```shell
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk floorplan
```

![Alt Text](Screenshots/fp1.png)

![Alt Text](Screenshots/fp2.png)

**Floorplan Result (GUI)**

```shell
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk gui_floorplan
```

This image shows the floorplan view in OpenROAD where you can see two macros placed: **DAC** and **PLL** after the floorplanning step.

![Alt Text](Screenshots/fp3.png)


## `VSDBabySoC — Placement`

```shell
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk place
```

![Alt Text](Screenshots/p1.png)

![Alt Text](Screenshots/p2.png)

**Placement Result (GUI)**

```shell
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk gui_place
```

This image shows the placement stage in OpenROAD with the **placement density** heat map enabled.

![Alt Text](Screenshots/p3.png)

This image shows a zoomed-in view of the Placement Density Heatmap after the placement stage:

- **Red regions** indicate areas with higher cell density, approaching 100%.
- **Green and blue regions** indicate moderate to low cell density.
- The highlighted instance (`_07289_`) displays details such as origin coordinates, site count, site spacing, and bounding box dimensions.

❗**Note:** In the floorplan stage, you do not see any placement density heat maps because standard cells have not yet been placed. The heat map will only appear after the placement step.

<ins>The placement density percentage is calculated as:</ins>

**Placement Density (%) = (Area Occupied by Cells ÷ Total Placement Area) × 100**

![Alt Text](Screenshots/p4.png)

This image shows the **Pin Density Heatmap** after the placement stage.

<ins>The pin density percentage is calculated as:</ins>

**Pin Density (%) = (Number of Pins in a Region ÷ Total Area of that Region) × 100**

![Alt Text](Screenshots/p5.png)


## `VSDBabySoC — Clock Tree Synthesis`

```shell
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk cts
```

![Alt Text](Screenshots/cts1.png)

![Alt Text](Screenshots/cts2.png)

**CTS Result (GUI)**

```shell
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk gui_cts
```
This image shows the **Clock Tree Viewer after Clock Tree Synthesis (CTS)**, illustrating the hierarchical structure of the clock network. The root node at the top represents the clock source (`pll/CLK`), and the branches show the inserted clock buffers used to distribute the clock signal across the design. The vertical axis represents the **clock arrival times (in nanoseconds)** at each stage. The endpoints at the bottom represent the registers (clock sinks), where the clock reaches after passing through multiple buffer levels. The balanced branching and closely aligned arrival times indicate **low clock skew and a well-optimized clock tree**.

![Alt Text](Screenshots/cts3.png)

This image shows the **Setup Timing Report**, presenting a list of timing paths with key metrics such as:

- **Required Time**
- **Arrival Time**
- **Slack**
- **Skew**
- **Logic Delay**
- **Logic Depth**
- **Fanout**

All paths have **positive slack**, confirming that the design meets **setup timing requirements**.
![Alt Text](Screenshots/cts4.png)


This image displays the **Hold Timing Report**, showing timing paths with details such as:

- **Required Time**
- **Arrival Time**
- **Slack**
- **Skew**
- **Logic Delay**
- **Fanout**

All paths listed have **positive slack**, indicating that the design meets **hold timing requirements** and is free from hold violations.
![Alt Text](Screenshots/cts5.png)

This image shows the **Setup Slack Histogram** after CTS. The histogram represents the distribution of endpoint slack values, all of which are positive, indicating that there are no setup timing violations.

![Alt Text](Screenshots/cts6.png)

This image shows the **Hold Slack Histogram** after CTS. The histogram represents the distribution of hold slack values for all endpoints. All values are positive, confirming that the design meets hold timing requirements without any violations.

![Alt Text](Screenshots/cts7.png)


Zoomed-in view of the design after CTS, showing inserted clock buffers and routing connections.
![Alt Text](Screenshots/cts8.png)
**CTS final report:**

```shell
gvim /home/veeraragavan/OpenROAD-flow-scripts/flow/reports/sky130hd/vsdbabysoc/base/4_cts_final.rpt
```

<details> <summary><strong>4_cts_final.rpt</strong></summary>

```

==========================================================================
cts final report_tns
--------------------------------------------------------------------------
tns max 0.00

==========================================================================
cts final report_wns
--------------------------------------------------------------------------
wns max 0.00

==========================================================================
cts final report_worst_slack
--------------------------------------------------------------------------
worst slack max 6.06

==========================================================================
cts final report_clock_min_period
--------------------------------------------------------------------------
clk period_min = 4.94 fmax = 202.47

==========================================================================
cts final report_clock_skew
--------------------------------------------------------------------------
Clock clk
   0.79 source latency core.CPU_src2_value_a3[11]$_DFF_P_/CLK ^
  -0.87 target latency core.CPU_src1_value_a3[18]$_DFF_P_/CLK ^
   0.00 CRPR
--------------
  -0.08 setup skew


==========================================================================
cts final report_checks -path_delay min
--------------------------------------------------------------------------
Startpoint: core.CPU_inc_pc_a2[3]$_DFF_P_
            (rising edge-triggered flip-flop clocked by clk)
Endpoint: core.CPU_inc_pc_a3[3]$_DFF_P_
          (rising edge-triggered flip-flop clocked by clk)
Path Group: clk
Path Type: min

Fanout     Cap    Slew   Delay    Time   Description
-----------------------------------------------------------------------------
                          0.00    0.00   clock clk (rise edge)
                          0.00    0.00   clock source latency
     1    0.31    0.00    0.00    0.00 ^ pll/CLK (avsdpll)
                                         CLK (net)
                  0.05    0.03    0.03 ^ clkbuf_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     8    0.22    0.23    0.27    0.30 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_0_CLK (net)
                  0.23    0.00    0.30 ^ clkbuf_3_0__f_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    12    0.22    0.23    0.33    0.62 ^ clkbuf_3_0__f_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_3_0__leaf_CLK (net)
                  0.23    0.00    0.63 ^ clkbuf_leaf_3_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     9    0.03    0.06    0.20    0.82 ^ clkbuf_leaf_3_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_leaf_3_CLK (net)
                  0.06    0.00    0.83 ^ core.CPU_inc_pc_a2[3]$_DFF_P_/CLK (sky130_fd_sc_hd__dfxtp_1)
     1    0.00    0.04    0.30    1.13 ^ core.CPU_inc_pc_a2[3]$_DFF_P_/Q (sky130_fd_sc_hd__dfxtp_1)
                                         core.CPU_inc_pc_a2[3] (net)
                  0.04    0.00    1.13 ^ core.CPU_inc_pc_a3[3]$_DFF_P_/D (sky130_fd_sc_hd__dfxtp_1)
                                  1.13   data arrival time

                          0.00    0.00   clock clk (rise edge)
                          0.00    0.00   clock source latency
     1    0.31    0.00    0.00    0.00 ^ pll/CLK (avsdpll)
                                         CLK (net)
                  0.05    0.03    0.03 ^ clkbuf_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     8    0.22    0.23    0.27    0.30 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_0_CLK (net)
                  0.23    0.00    0.30 ^ clkbuf_3_0__f_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    12    0.22    0.23    0.33    0.62 ^ clkbuf_3_0__f_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_3_0__leaf_CLK (net)
                  0.23    0.00    0.63 ^ clkbuf_leaf_110_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     9    0.06    0.08    0.21    0.84 ^ clkbuf_leaf_110_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_leaf_110_CLK (net)
                  0.08    0.00    0.84 ^ core.CPU_inc_pc_a3[3]$_DFF_P_/CLK (sky130_fd_sc_hd__dfxtp_1)
                          0.00    0.84   clock reconvergence pessimism
                         -0.03    0.82   library hold time
                                  0.82   data required time
-----------------------------------------------------------------------------
                                  0.82   data required time
                                 -1.13   data arrival time
-----------------------------------------------------------------------------
                                  0.31   slack (MET)



==========================================================================
cts final report_checks -path_delay max
--------------------------------------------------------------------------
Startpoint: core.CPU_src2_value_a3[13]$_DFF_P_
            (rising edge-triggered flip-flop clocked by clk)
Endpoint: core.CPU_src2_value_a3[24]$_DFF_P_
          (rising edge-triggered flip-flop clocked by clk)
Path Group: clk
Path Type: max

Fanout     Cap    Slew   Delay    Time   Description
-----------------------------------------------------------------------------
                          0.00    0.00   clock clk (rise edge)
                          0.00    0.00   clock source latency
     1    0.31    0.00    0.00    0.00 ^ pll/CLK (avsdpll)
                                         CLK (net)
                  0.05    0.03    0.03 ^ clkbuf_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     8    0.22    0.23    0.27    0.30 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_0_CLK (net)
                  0.23    0.00    0.30 ^ clkbuf_3_6__f_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    11    0.18    0.19    0.30    0.60 ^ clkbuf_3_6__f_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_3_6__leaf_CLK (net)
                  0.19    0.00    0.60 ^ clkbuf_leaf_70_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    10    0.04    0.06    0.19    0.80 ^ clkbuf_leaf_70_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_leaf_70_CLK (net)
                  0.06    0.00    0.80 ^ core.CPU_src2_value_a3[13]$_DFF_P_/CLK (sky130_fd_sc_hd__dfxtp_1)
     3    0.05    0.46    0.60    1.40 ^ core.CPU_src2_value_a3[13]$_DFF_P_/Q (sky130_fd_sc_hd__dfxtp_1)
                                         core.CPU_src2_value_a3[13] (net)
                  0.46    0.00    1.40 ^ _05535_/A (sky130_fd_sc_hd__inv_1)
     1    0.00    0.09    0.09    1.49 v _05535_/Y (sky130_fd_sc_hd__inv_1)
                                         _00102_ (net)
                  0.09    0.00    1.49 v _10833_/B (sky130_fd_sc_hd__ha_1)
    13    0.06    0.50    0.62    2.11 ^ _10833_/SUM (sky130_fd_sc_hd__ha_1)
                                         _00104_ (net)
                  0.50    0.00    2.11 ^ _05459_/D (sky130_fd_sc_hd__nand4_1)
     5    0.03    0.33    0.37    2.49 v _05459_/Y (sky130_fd_sc_hd__nand4_1)
                                         _01142_ (net)
                  0.33    0.00    2.49 v _08027_/C (sky130_fd_sc_hd__nor4_1)
     1    0.01    0.52    0.56    3.04 ^ _08027_/Y (sky130_fd_sc_hd__nor4_1)
                                         _03108_ (net)
                  0.52    0.00    3.04 ^ _08028_/C1 (sky130_fd_sc_hd__o311ai_0)
     1    0.00    0.19    0.26    3.30 v _08028_/Y (sky130_fd_sc_hd__o311ai_0)
                                         _03109_ (net)
                  0.19    0.00    3.30 v place284/A (sky130_fd_sc_hd__buf_4)
     2    0.02    0.04    0.21    3.51 v place284/X (sky130_fd_sc_hd__buf_4)
                                         net283 (net)
                  0.04    0.00    3.52 v _08206_/A1 (sky130_fd_sc_hd__a311oi_1)
     3    0.02    0.57    0.48    3.99 ^ _08206_/Y (sky130_fd_sc_hd__a311oi_1)
                                         _03283_ (net)
                  0.57    0.00    3.99 ^ _08214_/A2 (sky130_fd_sc_hd__o211ai_1)
     1    0.01    0.19    0.23    4.22 v _08214_/Y (sky130_fd_sc_hd__o211ai_1)
                                         _03291_ (net)
                  0.19    0.00    4.22 v _08215_/C_N (sky130_fd_sc_hd__nor3b_2)
     2    0.01    0.12    0.22    4.44 v _08215_/Y (sky130_fd_sc_hd__nor3b_2)
                                         _03292_ (net)
                  0.12    0.00    4.44 v _08216_/B (sky130_fd_sc_hd__nand2_1)
     1    0.00    0.08    0.09    4.53 ^ _08216_/Y (sky130_fd_sc_hd__nand2_1)
                                         _03293_ (net)
                  0.08    0.00    4.53 ^ _08224_/B1 (sky130_fd_sc_hd__a2bb2o_2)
     2    0.04    0.20    0.29    4.81 ^ _08224_/X (sky130_fd_sc_hd__a2bb2o_2)
                                         _03301_ (net)
                  0.20    0.00    4.82 ^ _09898_/B1_N (sky130_fd_sc_hd__a21boi_0)
     2    0.03    0.82    0.70    5.51 ^ _09898_/Y (sky130_fd_sc_hd__a21boi_0)
                                         _04589_ (net)
                  0.82    0.00    5.52 ^ _10251_/A1 (sky130_fd_sc_hd__o21a_1)
     1    0.00    0.05    0.22    5.74 ^ _10251_/X (sky130_fd_sc_hd__o21a_1)
                                         core.CPU_src2_value_a2[24] (net)
                  0.05    0.00    5.74 ^ core.CPU_src2_value_a3[24]$_DFF_P_/D (sky130_fd_sc_hd__dfxtp_1)
                                  5.74   data arrival time

                         11.00   11.00   clock clk (rise edge)
                          0.00   11.00   clock source latency
     1    0.31    0.00    0.00   11.00 ^ pll/CLK (avsdpll)
                                         CLK (net)
                  0.05    0.03   11.03 ^ clkbuf_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     8    0.22    0.23    0.27   11.30 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_0_CLK (net)
                  0.23    0.00   11.30 ^ clkbuf_3_4__f_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    17    0.24    0.25    0.34   11.64 ^ clkbuf_3_4__f_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_3_4__leaf_CLK (net)
                  0.25    0.00   11.64 ^ clkbuf_leaf_91_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    12    0.04    0.06    0.21   11.85 ^ clkbuf_leaf_91_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_leaf_91_CLK (net)
                  0.06    0.00   11.85 ^ core.CPU_src2_value_a3[24]$_DFF_P_/CLK (sky130_fd_sc_hd__dfxtp_1)
                          0.00   11.85   clock reconvergence pessimism
                         -0.05   11.80   library setup time
                                 11.80   data required time
-----------------------------------------------------------------------------
                                 11.80   data required time
                                 -5.74   data arrival time
-----------------------------------------------------------------------------
                                  6.06   slack (MET)



==========================================================================
cts final report_checks -unconstrained
--------------------------------------------------------------------------
Startpoint: core.CPU_src2_value_a3[13]$_DFF_P_
            (rising edge-triggered flip-flop clocked by clk)
Endpoint: core.CPU_src2_value_a3[24]$_DFF_P_
          (rising edge-triggered flip-flop clocked by clk)
Path Group: clk
Path Type: max

Fanout     Cap    Slew   Delay    Time   Description
-----------------------------------------------------------------------------
                          0.00    0.00   clock clk (rise edge)
                          0.00    0.00   clock source latency
     1    0.31    0.00    0.00    0.00 ^ pll/CLK (avsdpll)
                                         CLK (net)
                  0.05    0.03    0.03 ^ clkbuf_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     8    0.22    0.23    0.27    0.30 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_0_CLK (net)
                  0.23    0.00    0.30 ^ clkbuf_3_6__f_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    11    0.18    0.19    0.30    0.60 ^ clkbuf_3_6__f_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_3_6__leaf_CLK (net)
                  0.19    0.00    0.60 ^ clkbuf_leaf_70_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    10    0.04    0.06    0.19    0.80 ^ clkbuf_leaf_70_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_leaf_70_CLK (net)
                  0.06    0.00    0.80 ^ core.CPU_src2_value_a3[13]$_DFF_P_/CLK (sky130_fd_sc_hd__dfxtp_1)
     3    0.05    0.46    0.60    1.40 ^ core.CPU_src2_value_a3[13]$_DFF_P_/Q (sky130_fd_sc_hd__dfxtp_1)
                                         core.CPU_src2_value_a3[13] (net)
                  0.46    0.00    1.40 ^ _05535_/A (sky130_fd_sc_hd__inv_1)
     1    0.00    0.09    0.09    1.49 v _05535_/Y (sky130_fd_sc_hd__inv_1)
                                         _00102_ (net)
                  0.09    0.00    1.49 v _10833_/B (sky130_fd_sc_hd__ha_1)
    13    0.06    0.50    0.62    2.11 ^ _10833_/SUM (sky130_fd_sc_hd__ha_1)
                                         _00104_ (net)
                  0.50    0.00    2.11 ^ _05459_/D (sky130_fd_sc_hd__nand4_1)
     5    0.03    0.33    0.37    2.49 v _05459_/Y (sky130_fd_sc_hd__nand4_1)
                                         _01142_ (net)
                  0.33    0.00    2.49 v _08027_/C (sky130_fd_sc_hd__nor4_1)
     1    0.01    0.52    0.56    3.04 ^ _08027_/Y (sky130_fd_sc_hd__nor4_1)
                                         _03108_ (net)
                  0.52    0.00    3.04 ^ _08028_/C1 (sky130_fd_sc_hd__o311ai_0)
     1    0.00    0.19    0.26    3.30 v _08028_/Y (sky130_fd_sc_hd__o311ai_0)
                                         _03109_ (net)
                  0.19    0.00    3.30 v place284/A (sky130_fd_sc_hd__buf_4)
     2    0.02    0.04    0.21    3.51 v place284/X (sky130_fd_sc_hd__buf_4)
                                         net283 (net)
                  0.04    0.00    3.52 v _08206_/A1 (sky130_fd_sc_hd__a311oi_1)
     3    0.02    0.57    0.48    3.99 ^ _08206_/Y (sky130_fd_sc_hd__a311oi_1)
                                         _03283_ (net)
                  0.57    0.00    3.99 ^ _08214_/A2 (sky130_fd_sc_hd__o211ai_1)
     1    0.01    0.19    0.23    4.22 v _08214_/Y (sky130_fd_sc_hd__o211ai_1)
                                         _03291_ (net)
                  0.19    0.00    4.22 v _08215_/C_N (sky130_fd_sc_hd__nor3b_2)
     2    0.01    0.12    0.22    4.44 v _08215_/Y (sky130_fd_sc_hd__nor3b_2)
                                         _03292_ (net)
                  0.12    0.00    4.44 v _08216_/B (sky130_fd_sc_hd__nand2_1)
     1    0.00    0.08    0.09    4.53 ^ _08216_/Y (sky130_fd_sc_hd__nand2_1)
                                         _03293_ (net)
                  0.08    0.00    4.53 ^ _08224_/B1 (sky130_fd_sc_hd__a2bb2o_2)
     2    0.04    0.20    0.29    4.81 ^ _08224_/X (sky130_fd_sc_hd__a2bb2o_2)
                                         _03301_ (net)
                  0.20    0.00    4.82 ^ _09898_/B1_N (sky130_fd_sc_hd__a21boi_0)
     2    0.03    0.82    0.70    5.51 ^ _09898_/Y (sky130_fd_sc_hd__a21boi_0)
                                         _04589_ (net)
                  0.82    0.00    5.52 ^ _10251_/A1 (sky130_fd_sc_hd__o21a_1)
     1    0.00    0.05    0.22    5.74 ^ _10251_/X (sky130_fd_sc_hd__o21a_1)
                                         core.CPU_src2_value_a2[24] (net)
                  0.05    0.00    5.74 ^ core.CPU_src2_value_a3[24]$_DFF_P_/D (sky130_fd_sc_hd__dfxtp_1)
                                  5.74   data arrival time

                         11.00   11.00   clock clk (rise edge)
                          0.00   11.00   clock source latency
     1    0.31    0.00    0.00   11.00 ^ pll/CLK (avsdpll)
                                         CLK (net)
                  0.05    0.03   11.03 ^ clkbuf_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     8    0.22    0.23    0.27   11.30 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_0_CLK (net)
                  0.23    0.00   11.30 ^ clkbuf_3_4__f_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    17    0.24    0.25    0.34   11.64 ^ clkbuf_3_4__f_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_3_4__leaf_CLK (net)
                  0.25    0.00   11.64 ^ clkbuf_leaf_91_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    12    0.04    0.06    0.21   11.85 ^ clkbuf_leaf_91_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_leaf_91_CLK (net)
                  0.06    0.00   11.85 ^ core.CPU_src2_value_a3[24]$_DFF_P_/CLK (sky130_fd_sc_hd__dfxtp_1)
                          0.00   11.85   clock reconvergence pessimism
                         -0.05   11.80   library setup time
                                 11.80   data required time
-----------------------------------------------------------------------------
                                 11.80   data required time
                                 -5.74   data arrival time
-----------------------------------------------------------------------------
                                  6.06   slack (MET)



==========================================================================
cts final report_check_types -max_slew -max_cap -max_fanout -violators
--------------------------------------------------------------------------

==========================================================================
cts final max_slew_check_slack
--------------------------------------------------------------------------
0.21897073090076447

==========================================================================
cts final max_slew_check_limit
--------------------------------------------------------------------------
1.4979510307312012

==========================================================================
cts final max_slew_check_slack_limit
--------------------------------------------------------------------------
0.1462

==========================================================================
cts final max_fanout_check_slack
--------------------------------------------------------------------------
1.0000000150474662e+30

==========================================================================
cts final max_fanout_check_limit
--------------------------------------------------------------------------
1.0000000150474662e+30

==========================================================================
cts final max_capacitance_check_slack
--------------------------------------------------------------------------
0.01424864586442709

==========================================================================
cts final max_capacitance_check_limit
--------------------------------------------------------------------------
0.021067000925540924

==========================================================================
cts final max_capacitance_check_slack_limit
--------------------------------------------------------------------------
0.6763

==========================================================================
cts final max_slew_violation_count
--------------------------------------------------------------------------
max slew violation count 0

==========================================================================
cts final max_fanout_violation_count
--------------------------------------------------------------------------
max fanout violation count 0

==========================================================================
cts final max_cap_violation_count
--------------------------------------------------------------------------
max cap violation count 0

==========================================================================
cts final setup_violation_count
--------------------------------------------------------------------------
setup violation count 0

==========================================================================
cts final hold_violation_count
--------------------------------------------------------------------------
hold violation count 0

==========================================================================
cts final report_checks -path_delay max reg to reg
--------------------------------------------------------------------------
Startpoint: core.CPU_src2_value_a3[13]$_DFF_P_
            (rising edge-triggered flip-flop clocked by clk)
Endpoint: core.CPU_src2_value_a3[24]$_DFF_P_
          (rising edge-triggered flip-flop clocked by clk)
Path Group: clk
Path Type: max

  Delay    Time   Description
---------------------------------------------------------
   0.00    0.00   clock clk (rise edge)
   0.00    0.00   clock source latency
   0.00    0.00 ^ pll/CLK (avsdpll)
   0.30    0.30 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.31    0.60 ^ clkbuf_3_6__f_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.20    0.80 ^ clkbuf_leaf_70_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.00    0.80 ^ core.CPU_src2_value_a3[13]$_DFF_P_/CLK (sky130_fd_sc_hd__dfxtp_1)
   0.60    1.40 ^ core.CPU_src2_value_a3[13]$_DFF_P_/Q (sky130_fd_sc_hd__dfxtp_1)
   0.09    1.49 v _05535_/Y (sky130_fd_sc_hd__inv_1)
   0.62    2.11 ^ _10833_/SUM (sky130_fd_sc_hd__ha_1)
   0.37    2.49 v _05459_/Y (sky130_fd_sc_hd__nand4_1)
   0.56    3.04 ^ _08027_/Y (sky130_fd_sc_hd__nor4_1)
   0.26    3.30 v _08028_/Y (sky130_fd_sc_hd__o311ai_0)
   0.21    3.51 v place284/X (sky130_fd_sc_hd__buf_4)
   0.48    3.99 ^ _08206_/Y (sky130_fd_sc_hd__a311oi_1)
   0.23    4.22 v _08214_/Y (sky130_fd_sc_hd__o211ai_1)
   0.22    4.44 v _08215_/Y (sky130_fd_sc_hd__nor3b_2)
   0.09    4.53 ^ _08216_/Y (sky130_fd_sc_hd__nand2_1)
   0.29    4.81 ^ _08224_/X (sky130_fd_sc_hd__a2bb2o_2)
   0.70    5.51 ^ _09898_/Y (sky130_fd_sc_hd__a21boi_0)
   0.22    5.74 ^ _10251_/X (sky130_fd_sc_hd__o21a_1)
   0.00    5.74 ^ core.CPU_src2_value_a3[24]$_DFF_P_/D (sky130_fd_sc_hd__dfxtp_1)
           5.74   data arrival time

  11.00   11.00   clock clk (rise edge)
   0.00   11.00   clock source latency
   0.00   11.00 ^ pll/CLK (avsdpll)
   0.30   11.30 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.34   11.64 ^ clkbuf_3_4__f_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.21   11.85 ^ clkbuf_leaf_91_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.00   11.85 ^ core.CPU_src2_value_a3[24]$_DFF_P_/CLK (sky130_fd_sc_hd__dfxtp_1)
   0.00   11.85   clock reconvergence pessimism
  -0.05   11.80   library setup time
          11.80   data required time
---------------------------------------------------------
          11.80   data required time
          -5.74   data arrival time
---------------------------------------------------------
           6.06   slack (MET)



==========================================================================
cts final report_checks -path_delay min reg to reg
--------------------------------------------------------------------------
Startpoint: core.CPU_inc_pc_a2[3]$_DFF_P_
            (rising edge-triggered flip-flop clocked by clk)
Endpoint: core.CPU_inc_pc_a3[3]$_DFF_P_
          (rising edge-triggered flip-flop clocked by clk)
Path Group: clk
Path Type: min

  Delay    Time   Description
---------------------------------------------------------
   0.00    0.00   clock clk (rise edge)
   0.00    0.00   clock source latency
   0.00    0.00 ^ pll/CLK (avsdpll)
   0.30    0.30 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.33    0.62 ^ clkbuf_3_0__f_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.20    0.82 ^ clkbuf_leaf_3_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.00    0.83 ^ core.CPU_inc_pc_a2[3]$_DFF_P_/CLK (sky130_fd_sc_hd__dfxtp_1)
   0.30    1.13 ^ core.CPU_inc_pc_a2[3]$_DFF_P_/Q (sky130_fd_sc_hd__dfxtp_1)
   0.00    1.13 ^ core.CPU_inc_pc_a3[3]$_DFF_P_/D (sky130_fd_sc_hd__dfxtp_1)
           1.13   data arrival time

   0.00    0.00   clock clk (rise edge)
   0.00    0.00   clock source latency
   0.00    0.00 ^ pll/CLK (avsdpll)
   0.30    0.30 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.33    0.62 ^ clkbuf_3_0__f_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.22    0.84 ^ clkbuf_leaf_110_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.00    0.84 ^ core.CPU_inc_pc_a3[3]$_DFF_P_/CLK (sky130_fd_sc_hd__dfxtp_1)
   0.00    0.84   clock reconvergence pessimism
  -0.03    0.82   library hold time
           0.82   data required time
---------------------------------------------------------
           0.82   data required time
          -1.13   data arrival time
---------------------------------------------------------
           0.31   slack (MET)



==========================================================================
cts final critical path target clock latency max path
--------------------------------------------------------------------------
0

==========================================================================
cts final critical path target clock latency min path
--------------------------------------------------------------------------
0

==========================================================================
cts final critical path source clock latency min path
--------------------------------------------------------------------------
0

==========================================================================
cts final critical path delay
--------------------------------------------------------------------------
5.7372

==========================================================================
cts final critical path slack
--------------------------------------------------------------------------
6.0609

==========================================================================
cts final slack div critical path delay
--------------------------------------------------------------------------
105.642125

==========================================================================
cts final report_power
--------------------------------------------------------------------------
Group                  Internal  Switching    Leakage      Total
                          Power      Power      Power      Power (Watts)
----------------------------------------------------------------
Sequential             4.37e-03   4.41e-04   9.26e-09   4.81e-03  40.3%
Combinational          8.42e-04   1.89e-03   9.76e-09   2.74e-03  22.9%
Clock                  2.50e-03   1.90e-03   2.05e-09   4.40e-03  36.8%
Macro                  0.00e+00   0.00e+00   0.00e+00   0.00e+00   0.0%
Pad                    0.00e+00   0.00e+00   0.00e+00   0.00e+00   0.0%
----------------------------------------------------------------
Total                  7.71e-03   4.23e-03   2.11e-08   1.19e-02 100.0%
                          64.6%      35.4%       0.0%
```
</details>


## `VSDBabySoC — Routing`

```shell
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk route
```

![Alt Text](Screenshots/rt1.png)
![Alt Text](Screenshots/rt2.png)

**Routing Result (GUI)**

```shell
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk gui_route
```
This image shows the **post-routing stage**. The highlighted net `OUT` is fully routed, with its details such as signal type, wire type, and bounding box displayed in the Inspector.

![Alt Text](Screenshots/rt3.png)

![Alt Text](Screenshots/rt4.png)

This image shows the **Routing Congestion Heatmap** after the routing stage. Areas with higher congestion are highlighted in **red**, while green regions indicate lower congestion. The highlighted net `_01595_` is fully routed, and its properties such as bounding box and connectivity details are shown in the Inspector.

![Alt Text](Screenshots/rt5.png)

#### <ins>Formula for Congestion:</ins>

The routing congestion percentage is calculated as:

**Congestion (%) = (Used Routing Tracks ÷ Available Routing Tracks) × 100**

Where:
- **Used Routing Tracks** = Number of tracks occupied by wires in a specific region.
- **Available Routing Tracks** = Total routing capacity of that region.

![Alt Text](Screenshots/rt6.png)

## ` VSDBabySoC — 🔄 Convert .odb` to `.def` in OpenROAD`

Follow the steps below to export a DEF file from an existing OpenDB (`.odb`) database.

```shell
cd ~/OpenROAD-flow-scripts
source env.sh
cd flow
openroad
# Load the .odb database file
read_db /home/veeraragavan/OpenROAD-flow-scripts/flow/results/sky130hd/vsdbabysoc/base/5_route.odb
# Write out the DEF file
write_def /home/veeraragavan/OpenROAD-flow-scripts/flow/results/sky130hd/vsdbabysoc/base/5_route.def
```
![Alt Text](Screenshots/odb2def1.png)

```shell
gvim /home/veeraragavan/OpenROAD-flow-scripts/flow/results/sky130hd/vsdbabysoc/base/5_route.def
```
![Alt Text](Screenshots/odb2def2.png)

## ` VSDBabySoC — post_route SPEF generation`

This section covers the step-by-step procedure to generate the **post-route Standard Parasitic Exchange Format (SPEF)** and **post-placement Verilog netlist** for the `VSDBabySoC` design using OpenROAD. These outputs are essential for accurate timing analysis and signoff after the routing stage. The SPEF file captures parasitic RC effects from the physical layout, while the updated Verilog reflects the final net connections post-placement and routing.

### Step 1: Launch OpenROAD

Before starting OpenROAD, set up the environment and navigate to the flow directory:

```bash
cd ~/OpenROAD-flow-scripts
source env.sh
cd flow/
openroad
```

![Alt Text](Screenshots/openroad.png)

### Step 2: Load Design and Technology Files

Once inside the OpenROAD shell, run the following commands in sequence to load the required design and technology data for VSDBabySoC:

These files describe the physical dimensions and metal/via layers for standard cells and macros:
```shell
read_lef /home/veeraragavan/OpenROAD-flow-scripts/flow/designs/sky130hd/vsdbabysoc/lef/sky130hd.lef
read_lef /home/veeraragavan/OpenROAD-flow-scripts/flow/designs/sky130hd/vsdbabysoc/lef/avsdpll.lef
read_lef /home/veeraragavan/OpenROAD-flow-scripts/flow/designs/sky130hd/vsdbabysoc/lef/avsddac.lef
```

This file contains timing and power data for the standard cells:
```shell
read_liberty /home/veeraragavan/OpenROAD-flow-scripts/flow/designs/sky130hd/vsdbabysoc/lib/avsddac.lef
```

The DEF file represents the post-route physical layout of the design:
```shell
read_def /home/veeraragavan/OpenROAD-flow-scripts/flow/results/sky130hd/vsdbabysoc/base/5_2_route.def
```

![Alt Text](Screenshots/loadtech.png)

### `Step 3: RC Extraction and Output Generation`

After loading the LEF, Liberty, and DEF files, run the following commands to define the process corner and extract parasitics using the available `.calibre`-format model:

#### 🔹 1. Define Process Corner
Set the process corner using the available Calibre-based extraction rules file:

```tcl
define_process_corner -ext_model_index 0 /home/veeraragavan/OpenROAD-flow-scripts/external-resources/open_pdks/sky130/openlane/rules.openrcx.sky130A.nom.calibre
```

#### 🔹 2. Extract Parasitics

Run parasitic extraction using the same file:

```shell
extract_parasitics -ext_model_file /home/veeraragavan/OpenROAD-flow-scripts/external-resources/open_pdks/sky130/openlane/rules.openrcx.sky130A.nom.calibre
```

#### 🔹 3. Write SPEF File
Save the extracted parasitics:

```shell
write_spef /home/veeraragavan/OpenROAD-flow-scripts/flow/designs/sky130hd/vsdbabysoc/vsdbabysoc.spef
```

#### 🔹 4. Write Post-Placement Verilog Netlist
Save the netlist after placement and routing:

```shell
write_verilog /home/veeraragavan/OpenROAD-flow-scripts/flow/designs/sky130hd/vsdbabysoc/vsdbabysoc_post_place.v
```

![Alt Text](Screenshots/spef1.png)

The Standard Parasitic Exchange Format (SPEF) file captures the resistance and capacitance (RC) parasitics of interconnects extracted from the routed layout. This file is essential for accurate post-route static timing analysis (STA) as it models real-world wire delays caused by metal layers and vias. Tools like OpenSTA read the SPEF file to compute timing paths that reflect true physical behavior after routing. Generating and inspecting the SPEF ensures that your design is signoff-ready with precise timing estimates.

```shell
gvim /home/veeraragavan/OpenROAD-flow-scripts/flow/designs/sky130hd/vsdbabysoc/vsdbabysoc.spef
```

![Alt Text](Screenshots/spef2.png)

The post-placement Verilog netlist represents the logical connectivity of the design after placement and routing have been completed. This version of the netlist includes any modifications made by optimization or physical synthesis during the backend flow and ensures consistency with the final layout. It is used in downstream verification flows and enables correlation between logical simulation and physical implementation. Writing this netlist is crucial for timing closure and for validating the final connectivity of the design.

```shell
gvim /home/veeraragavan/OpenROAD-flow-scripts/flow/designs/sky130hd/vsdbabysoc/vsdbabysoc_post_place.v
```

![Alt Text](Screenshots/v1.png)


## `VSDBabySoC — Post-Route Timing Closure`

### 🎯 Objective
To perform Post-Layout Static Timing Analysis (STA) using the SPEF extracted after routing in Week 7, analyze timing across multiple PVT corners, and compare the results with Week 3 post-synthesis timing.

- Post-route **gate-level netlist**
- **SPEF parasitics**
- **PVT corner libraries**
- **SDC timing constraints**

This analysis measures how routing affects timing and validates the design for tape-out.

---

### 🔍 `Why This Task Is Important`

This is the final stage of the complete ASIC flow:

`RTL → Synthesis → Floorplan → Placement → CTS → Routing → SPEF → Post-Layout STA`

Post-route STA reveals:

- Impact of **RC parasitics (from SPEF)** on timing
- Variation in timing across **PVT corners**
- Difference between **pre-route vs post-route timing**
- Whether the SoC is **ready for fabrication**

---
### 🚀 Task Flow
---

### 1️⃣ `Load Post-Route Design into OpenSTA`

Load the required data:

- Liberty files (`TT`, `SS`, `FF`) 
   - avsddac.lib
   - avsdpll.lib
   - sky130_fd_sc_hd__tt_025C_1v80.lib 
- Post_route_netlist 
   - vsdbabysoc_post_route_netlist.v
- SPEF 
   - vsdbabysoc.spef
- SDC 
   - vsdbabysoc_post_cts.sdc
  

### TCL Script

<details> <summary><strong>sta_across_pvt_route.tcl</strong></summary>

 set list_of_lib_files(1) "sky130_fd_sc_hd__tt_025C_1v80.lib"
 set list_of_lib_files(2) "sky130_fd_sc_hd__ff_100C_1v65.lib"
 set list_of_lib_files(3) "sky130_fd_sc_hd__ff_100C_1v95.lib"
 set list_of_lib_files(4) "sky130_fd_sc_hd__ff_n40C_1v56.lib"
 set list_of_lib_files(5) "sky130_fd_sc_hd__ff_n40C_1v65.lib"
 set list_of_lib_files(6) "sky130_fd_sc_hd__ff_n40C_1v76.lib"
 set list_of_lib_files(7) "sky130_fd_sc_hd__ss_100C_1v40.lib"
 set list_of_lib_files(8) "sky130_fd_sc_hd__ss_100C_1v60.lib"
 set list_of_lib_files(9) "sky130_fd_sc_hd__ss_n40C_1v28.lib"
 set list_of_lib_files(10) "sky130_fd_sc_hd__ss_n40C_1v35.lib"
 set list_of_lib_files(11) "sky130_fd_sc_hd__ss_n40C_1v40.lib"
 set list_of_lib_files(12) "sky130_fd_sc_hd__ss_n40C_1v44.lib"
 set list_of_lib_files(13) "sky130_fd_sc_hd__ss_n40C_1v76.lib"

 read_liberty /home/veeraragavan/VSD_Soc_TapeOut_Program/week8/Post_route_STA/lib/avsdpll.lib
 read_liberty /home/veeraragavan/VSD_Soc_TapeOut_Program/week8/Post_route_STA/lib/avsddac.lib
 
 set PDK_ROOT "/usr/local/share/pdk"
 
 for {set i 1} {$i <= [array size list_of_lib_files]} {incr i} {
 read_liberty $PDK_ROOT/sky130A/libs.ref/sky130_fd_sc_hd/lib/$list_of_lib_files($i) 
 read_verilog /home/veeraragavan/VSD_Soc_TapeOut_Program/week8/Post_route_STA/src/vsdbabysoc_post_route_netlist.v
 link_design vsdbabysoc
 current_design
 read_sdc /home/veeraragavan/VSD_Soc_TapeOut_Program/week8/Post_route_STA/src/vsdbabysoc_post_cts.sdc
 read_spef /home/veeraragavan/VSD_Soc_TapeOut_Program/week8/Post_route_STA/src/vsdbabysoc.spef
 check_setup -verbose
 report_checks -path_delay min_max -fields {nets cap slew input_pins fanout} -digits {4} > /home/veeraragavan/VSD_Soc_TapeOut_Program/week8/Post_route_STA/output/min_max_$list_of_lib_files($i).txt

 exec echo "$list_of_lib_files($i)" >> /home/veeraragavan/VSD_Soc_TapeOut_Program/week8/Post_route_STA/output/sta_worst_max_slack.txt
 report_worst_slack -max -digits {4} >> /home/veeraragavan/VSD_Soc_TapeOut_Program/week8/Post_route_STA/output/sta_worst_max_slack.txt

 exec echo "$list_of_lib_files($i)" >> /home/veeraragavan/VSD_Soc_TapeOut_Program/week8/Post_route_STA/output/sta_worst_min_slack.txt
 report_worst_slack -min -digits {4} >> /home/veeraragavan/VSD_Soc_TapeOut_Program/week8/Post_route_STA/output/sta_worst_min_slack.txt

 exec echo "$list_of_lib_files($i)" >> /home/veeraragavan/VSD_Soc_TapeOut_Program/week8/Post_route_STA/output/sta_tns.txt
 report_tns -digits {4} >> /home/veeraragavan/VSD_Soc_TapeOut_Program/week8/Post_route_STA/output/sta_tns.txt

 exec echo "$list_of_lib_files($i)" >> /home/veeraragavan/VSD_Soc_TapeOut_Program/week8/Post_route_STA/output/sta_wns.txt
 report_wns -digits {4} >> /home/veeraragavan/VSD_Soc_TapeOut_Program/week8/Post_route_STA/output/sta_wns.txt
 }
</details>

### 2️⃣ Run Post-Route STA Across PVT Corners

Run multi-corner timing using the provided TCL script.

```shell
 % source /home/veeraragavan/VSD_Soc_TapeOut_Program/week8/Post_route_STA/src/sta_across_pvt_route.tcl
```

![Alt Text](Screenshots/R_sta1.png)

After running the STA script, you can navigate to the `output` directory to see all the generated timing reports. This includes detailed path delay reports for each library corner (`min_max_*.txt`), worst setup and hold slack summaries (`sta_worst_max_slack.txt and sta_worst_min_slack.txt`), as well as total negative slack (sta_tns.txt) and worst negative slack (`sta_wns.txt`). These files provide a complete overview of the BabySoC design’s timing performance after routing.

![Alt Text](Screenshots/R_sta2.png)

### 3️⃣ Generate Timing Reports & Graphs

Here is a tabulated view of the key timing results generated by the STA script.

| PVT Corner (Lib File)  | **WNS (Setup)**            | **TNS (Setup)**               | **WHS (Hold)**  | **THS (Hold)**  |
| ---------------------- | -------------------------- | ----------------------------- | --------------- | ----------------|
| **tt_025C_1v80**       | 0.0000                     | 0.0000                        | 0.3102          | 0.0000          |
| **ff_100C_1v65**       | 0.0000                     | 0.0000                        | 0.2503          | 0.0000          |
| **ff_100C_1v95**       | 0.0000                     | 0.0000                        | 0.1983          | 0.0000          |
| **ff_n40C_1v56**       | 0.0000                     | 0.0000                        | 0.2901          | 0.0000          |
| **ff_n40C_1v65**       | 0.0000                     | 0.0000                        | 0.2559          | 0.0000          |
| **ff_n40C_1v76**       | 0.0000                     | 0.0000                        | 0.2264          | 0.0000          |
| **ss_100C_1v40**       | **–3.6516** *(VIOLATION)*  | **–678.1725** *(VIOLATION)*   | 0.8942          | 0.0000          |
| **ss_100C_1v60**       | 0.0000                     | 0.0000                        | 0.6345          | 0.0000          |
| **ss_n40C_1v28**       | **–32.8690** *(VIOLATION)* | **–15110.6885** *(VIOLATION)* | 1.7456          | 0.0000          |
| **ss_n40C_1v35**       | **–18.1312** *(VIOLATION)* | **–7144.3579** *(VIOLATION)*  | 1.3038          | 0.0000          |
| **ss_n40C_1v40**       | **–12.1683** *(VIOLATION)* | **–3859.9375** *(VIOLATION)*  | 1.0915          | 0.0000          |
| **ss_n40C_1v44**       | **–8.7039** *(VIOLATION)*  | **–2289.1768** *(VIOLATION)*  | 0.9627          | 0.0000          |
| **ss_n40C_1v76**       | 0.0000                     | 0.0000                        | 0.4868          | 0.0000          |


### 📈 Comparison Graphs

Here is a graph showing the comparison of `worst-case hold slack` post-synthesis vs post-routing for the BabySoC design.

![Alt Text](Screenshots/WHS.png)

Here is a graph showing the comparison of `worst-case setup slack` post-synthesis vs post-routing for the BabySoC design.

![Alt Text](Screenshots/THS.png)

Here is a graph showing the comparison of `WNS` post-synthesis vs post-routing for the BabySoC design.

![Alt Text](Screenshots/WNS.png)

Here is a graph showing the comparison of `TNS` post-synthesis vs post-routing for the BabySoC design.

![Alt Text](Screenshots/TNS.png)


### 4️⃣ Week 3 vs Week 8 Timing Comparison

The table below compares **Post-Synthesis Timing (Week 3)** with **Post-Route Timing (Week 8)** across all PVT corners using SPEF-annotated STA.

### 📊 Timing Summary Across All PVT Corners (Post-Route STA)


| PVT Corner        | WNS(Week3) | WNS(Week8) | TNS(Week3)  | TNS(Week8)  | WHS(Week3)  | WHS(Week8) | THS(Week3) | THS(Week8) |
|-------------------|-----------:|-----------:|------------:|------------:|------------:|-----------:|-----------:|-----------:|
| tt_025C_1v80      | 0.0000     | 0.0000     | 0.0000      | 0.0000      | 0.3096      | 0.3102     | 0.0000     | 0.0000     |
| ff_100C_1v65      | 0.0000     | 0.0000     | 0.0000      | 0.0000      | 0.2491      | 0.2503     | 0.0000     | 0.0000     |
| ff_100C_1v95      | 0.0000     | 0.0000     | 0.0000      | 0.0000      | 0.1960      | 0.1983     | 0.0000     | 0.0000     |
| ff_n40C_1v56      | 0.0000     | 0.0000     | 0.0000      | 0.0000      | 0.2915      | 0.2901     | 0.0000     | 0.0000     |
| ff_n40C_1v65      | 0.0000     | 0.0000     | 0.0000      | 0.0000      | 0.2551      | 0.2559     | 0.0000     | 0.0000     |
| ff_n40C_1v76      | 0.0000     | 0.0000     | 0.0000      | 0.0000      | 0.2243      | 0.2264     | 0.0000     | 0.0000     |
| ss_100C_1v40      | -13.0402   | -3.6516    | -7518.3794  | -678.1725   | 0.9053      | 0.8942     | 0.0000     | 0.0000     |
| ss_100C_1v60      | -6.2777    | 0.0000     | -2911.6208  | 0.0000      | 0.6420      | 0.6345     | 0.0000     | 0.0000     |
| ss_n40C_1v28      | -52.9031   | -32.8690   | -36775.3008 | -15110.6885 | 1.8296      | 1.7456     | 0.0000     | 0.0000     |
| ss_n40C_1v35      | -33.1984   | -18.1312   | -23279.6816 | -7144.3579  | 1.3475      | 1.3038     | 0.0000     | 0.0000     |
| ss_n40C_1v40      | -24.6564   | -12.1683   | -17173.1113 | -3859.9375  | 1.1249      | 1.0915     | 0.0000     | 0.0000     |
| ss_n40C_1v44      | -19.9610   | -8.7039    | -13603.9434 | -2289.1768  | 0.9909      | 0.9627     | 0.0000     | 0.0000     |
| ss_n40C_1v76      | -3.9606    | 0.0000     | -1905.4320  | 0.0000      | 0.5038      | 0.4868     | 0.0000     | 0.0000     |


### 5️⃣ Interpret Results

### ⭐ Post-Synthesis vs Post-Route Timing Analysis (VSDBabySoC)
### Key Differences: Post-Synthesis vs Post-Route STA
| Aspect                        | Post-Synthesis Timing Analysis              | Post-Route Timing Analysis                                                |
| ----------------------------- | ------------------------------------------- | ------------------------------------------------------------------------- |
| **Clock Network**             | Ideal clock, **0 skew**, no insertion delay | Real propagated clock tree with **0.75–0.78 ns** latency and skew         |
| **Interconnect Modeling**     | Fanout-based delay, no parasitics           | Real RC parasitics from SPEF (resistance, capacitance, coupling)          |
| **Min (Hold) Path Delay**     | Data arrival: **0.2749 ns**                 | Data arrival: **1.054 ns** (slower due to routing parasitics)             |
| **Min Path Slack**            | **+0.3096 ns**                              | **+0.3102 ns** (similar slack but different actual delays)                |
| **Max (Setup) Path Delay**    | Data arrival: **9.7554 ns**                 | Data arrival: **5.6038 ns** (routing created a *different* critical path) |
| **Max Path Slack**            | **+1.1060 ns**                              | **+6.0345 ns** (post-route path is far faster / shorter)                  |
| **Critical Path Behavior**    | Driven by fanout-heavy cell (~242 loads)    | More localized path after placement and buffering                         |
| **Clock Latency Considered?** | No (ideal)                                  | Yes — propagated clock delay (~0.78 ns)                                   |
| **Accuracy vs Silicon**       | ~70–80% correlation                         | ~95–98% correlation                                                       |
| **Path Type**                 | Logical placement only                      | Physical placement + routing topology                                     |




