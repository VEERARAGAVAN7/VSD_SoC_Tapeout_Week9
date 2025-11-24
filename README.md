# 🧠 Final Documentation — RTL to GDS Completion of VSDBabySoC — Week 9

<div align="center">

![RISC-V](https://img.shields.io/badge/RISC--V-SoC%20Tapeout-blue?style=for-the-badge&logo=riscv)
![VSD](https://img.shields.io/badge/VSD-Program-orange?style=for-the-badge)
![Sky130](https://img.shields.io/badge/SkyWater-130nm-green?style=for-the-badge)
![OpenROAD](https://img.shields.io/badge/OpenROAD-RTRL2GDS-purple?style=for-the-badge)
![India](https://img.shields.io/badge/Made%20in-India-saffron?style=for-the-badge)

</div>

<div align="center">

💡 RTL → 🧩 Netlist → 🏗️ Floorplan → 📌 Placement → 🌳 CTS → 🛣️ Routing → 🧮 STA → 📦 GDS Export

</div>

---

## 📅 Week 9 — Final Documentation of the Complete RTL-to-GDS Flow

Week 9 is fully dedicated to consolidating every stage of the VSDBabySoC development—from RTL design to final GDS.  
This final documentation captures **all implementation steps**, **verification stages**, and **physical design results**, forming a complete tape-out ready report.

---

## 🧪 Introduction to the VSDBabySoC

The VSDBabySoC integrates a compact RISC-V core, supporting logic, and peripheral modules inside a Sky130-based SoC environment.  
This section explains the SoC architecture, module interactions, signal flow, and the purpose of each component, laying the foundation for the RTL-to-GDS design journey.

---

## 🧪 VSDBabySoC – Pre-Synthesis Simulation

Before synthesizing the RTL, the design was validated using functional testbenches.  
This simulation ensured that the input–output behavior of the RTL matched the expected logic, confirming correctness in instruction execution and control-datapath behavior.

---

## 🧪 VSDBabySoC – Synthesis Process

RTL was converted into a gate-level netlist using technology-mapped Sky130 standard cells.  
This stage included logic optimization, constraint handling, and report generation for timing, area, and hierarchy. The synthesized netlist became the foundation for backend flow.

---

## 🧪 VSDBabySoC – Post-Synthesis Simulation (GLS)

Gate-Level Simulation validated the synthesized netlist with real cell delays.  
This confirmed that synthesis preserved functionality and that the gate-level model produced the same outputs as the RTL under actual timing behavior.

---

## 🧪 VSDBabySoC – Static Timing Analysis (STA)

Timing analysis identified setup/hold slacks, critical paths, and timing margin before layout.  
This stage created the “baseline timings” used later for comparison with post-layout timing, helping understand how routing parasitics affect performance.


### 🧪 VSDBabySoC — Floorplan

Die size, core area, macro placement, pin arrangement, and power grid configuration were finalized during floorplanning.  
A well-structured floorplan ensured low congestion, clean routing channels, and stable power distribution.

---

### 🧪 VSDBabySoC — Placement

Standard cells were arranged to minimize wirelength and avoid routing congestion.  
Both global and detailed placement ensured legal positioning, optimized timing, and improved routing efficiency.

---

### 🧪 VSDBabySoC — Clock Tree Synthesis

CTS built a balanced, low-skew clock distribution network using clock buffers and routing strategies.  
This ensured that all sequential elements received the clock signal within controlled insertion delay and skew limits.

---

### 🧪 VSDBabySoC — Routing

Routing connected all placed cells and macros using allowed metal layers while meeting DRC rules.  
The final routed layout was congestion-free, timing-aware, and ready for signoff checks.

---

## 🧪 VSDBabySoC — Convert .odb to .def in OpenROAD

The routed OpenDB database was exported into .def format to enable external visualization and additional verification stages.  
This conversion is essential for LVS, DRC, and other post-route processing tools.

---

## 🧪 VSDBabySoC — Post-Route SPEF Generation

Extracted parasitics (R, C, coupling effects) from the routed design into SPEF format.  
These parasitics provide real wire delays and are essential for accurate post-layout STA.

---

## 🧪 VSDBabySoC — Post-Route Timing Closure

Post-layout STA was performed using parasitics from the SPEF file across multiple PVT corners.  
Setup and hold slacks were validated, ensuring timing closure before tape-out.

---

## 🔗 Final Documentation File  
👉 [VSDBabySoC RTL-to-GDS documentation](https://github.com/VEERARAGAVAN7/VSD_SoC_Tapeout_Week9/blob/main/week9_final_documentation.md)


---

## 🙏 Acknowledgment

<div align="center">

### 🏆 Program Leadership & Support  
Thanks to **Kunal Ghosh** and the **VSD Team** for consistent guidance throughout the RTL-to-GDS journey.

</div>

---

## 📈 Weekly Progress Tracker

![Week 1](https://img.shields.io/badge/Week%201-RTL%20Foundations-success?style=flat-square)
![Week 2](https://img.shields.io/badge/Week%202-SoC%20Design%20Flow-success?style=flat-square)
![Week 3](https://img.shields.io/badge/Week%203-Pre--Route%20STA-success?style=flat-square)
![Week 4](https://img.shields.io/badge/Week%204-CMOS%20Design-success?style=flat-square)
![Week 5](https://img.shields.io/badge/Week%205-Floorplan%20%26%20Placement-success?style=flat-square)
![Week 6](https://img.shields.io/badge/Week%206-Physical%20Design-success?style=flat-square)
![Week 7](https://img.shields.io/badge/Week%207-Routing%20%26%20SPEF-success?style=flat-square)
![Week 8](https://img.shields.io/badge/Week%208-Post%20Layout%20STA-success?style=flat-square)
![Week 9](https://img.shields.io/badge/Week%209-Final%20Documentation-brightgreen?style=flat-square)

---

### 🚀 Final Step…

Next: **Complete Tape-Out Package → DRC → LVS → GDS → Submission** 🚀

---

**🔗 Program Links**

[![VSD Website](https://img.shields.io/badge/VSD-Official%20Website-blue?style=flat-square)](https://vsdiat.vlsisystemdesign.com/)  
[![Sky130](https://img.shields.io/badge/Open%20PDK-Sky130-green?style=flat-square)](https://github.com/google/skywater-pdk)  
[![Efabless](https://img.shields.io/badge/Efabless-Platform-orange?style=flat-square)](https://efabless.com/)

**👨‍💻 Participant:** [VEERARAGAVAN7](https://github.com/VEERARAGAVAN7)

