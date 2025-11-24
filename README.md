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

This week focuses on compiling and documenting **all stages** of the VSDBabySoC design flow—from RTL code to final GDS.  
Every major step performed in Weeks 1–8 is consolidated into a single, clean, tape-out-ready documentation file.

---

## 🧪 Introduction to the VSDBabySoC

### 📘 Summary  
Provided an overview of the VSDBabySoC design, architecture, modules, and integration structure.  
Covered the motivation, design goals, and functional blocks powering the RISC-V subsystem.

---

## 🧪 VSDBabySoC – Pre-Synthesis Simulation

### 📘 Summary  
Performed functional verification using testbenches to validate RTL behavior.  
Ensured that the input stimuli produced correct outputs and that the design matched the expected ISA flow.

---

## 🧪 VSDBabySoC – Synthesis Process

### 📘 Summary  
Converted RTL to gate-level netlist using open-source tools.  
Technology-mapped logical cells to Sky130 standard cells and generated area/timing reports.

---

## 🧪 VSDBabySoC – Post-Synthesis Simulation (GLS)

### 📘 Summary  
Simulated the gate-level netlist to verify correctness after synthesis.  
GLS ensured logical equivalence and confirmed that the timing-annotated netlist produced the same outputs as RTL.

---

## 🧪 VSDBabySoC – Static Timing Analysis (STA)

### 📘 Summary  
Analyzed setup and hold timing based on synthesized netlist before layout.  
This established the timing baseline for later comparison with post-layout STA.

---

## 🧪 VSDBabySoC – Physical Design of VSDBabySoC in OpenROAD

### Contents of Config.mk  
Provided complete configuration settings including library paths, macro placements, constraints, and environment setup.

---

### 🧪 VSDBabySoC — Synthesis

### 📘 Summary  
Executed physical design synthesis, generating technology-mapped netlists.  
Verified initial timing and ensured all constraints were met.

---

### 🧪 VSDBabySoC — Floorplan

### 📘 Summary  
Defined die/core area, macro placement, pin arrangement, power grid planning, and utilized routing channels efficiently.

---

### 🧪 VSDBabySoC — Placement

### 📘 Summary  
Performed global and detailed placement of all standard cells.  
Ensured optimal cell spacing and minimized wirelength, congestion, and routing violations.

---

### 🧪 VSDBabySoC — Clock Tree Synthesis

### 📘 Summary  
Built a balanced, low-skew clock network.  
Inserted buffers to distribute clock signals uniformly across the design.

---

### 🧪 VSDBabySoC — Routing

### 📘 Summary  
Completed global and detailed routing for the entire SoC.  
Connected all nets using metal layers while obeying DRC rules.

---

## 🧪 VSDBabySoC — Convert .odb to .def in OpenROAD

### 📘 Summary  
Converted the OpenDB representation to DEF to enable cross-tool visualization and downstream physical verification.

---

## 🧪 VSDBabySoC — Post-Route SPEF Generation

### 📘 Summary  
Extracted parasitics—wire resistance, capacitance, and coupling—from the final routed design into SPEF format.  
This SPEF enabled real, parasitic-aware timing analysis.

---

## 🧪 VSDBabySoC — Post-Route Timing Closure

### 📘 Summary  
Performed final STA across real routed nets using SPEF annotation.  
Verified setup/hold timing for all corner cases and ensured slack margins were met before tape-out.

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

