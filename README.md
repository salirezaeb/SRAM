# 📦 SRAM Cell Design & Simulation – 6T and 10T Architectures  
**Technology Node: 45nm | Tool: HSPICE | Model: Predictive Technology Models (PTM)**

This repository presents a comprehensive implementation and analysis of two fundamental SRAM bitcell architectures: the standard **6-transistor (6T)** cell and a more robust **10-transistor (10T)** variant. Simulations were conducted using **HSPICE** in conjunction with **45nm Predictive Technology Models (PTM)**, with a focus on evaluating **storage characteristics**, **read/write behavior**, **Static Noise Margin (SNM)**, and **dynamic power dissipation**.

📄 For waveform outputs, SNM butterfly curves, and schematic visuals, refer to the accompanying report: `The simulation of the SRAM.pdf`.

---

## 📁 Repository Contents

| File                          | Description                                              |
|------------------------------|----------------------------------------------------------|
| `sram_6t.sp`                  | SPICE netlist for the 6T SRAM bitcell                    |
| `sram_10t.sp`                 | SPICE netlist for the 10T SRAM bitcell                   |
| `45nm.pm`                     | PTM device model file for the 45nm CMOS technology node  |
| `The simulation of the SRAM.pdf` | PDF report containing circuits, waveforms, and SNM plots |
| `README.md`                   | Project documentation (this file)                        |

---

## 🧠 Technical Overview: SRAM Bitcells

**Static Random Access Memory (SRAM)** stores binary data using cross-coupled CMOS inverters. The **6T SRAM** is an area-efficient design but exhibits sensitivity to noise, particularly during read operations. The **10T SRAM** architecture introduces additional transistors to decouple the read path, thereby improving noise immunity and operational robustness.

---

## 🔹 6T SRAM Cell

- **Configuration**: Two cross-coupled CMOS inverters and two NMOS access transistors  
- **Control Scheme**: Single wordline (WL) and complementary bitlines (BL, BLB)  
- **Simulation Type**:  
  - `.TRAN 1ps 9ns` for transient behavior  
- **Input Stimuli**: Pulsed signals on WL, BL, and BLB simulate read/write phases  
- **Device Dimensions**:  
  - **NMOS**: Width = 45nm, Length = 45nm  
  - **PMOS**: Width = 90nm, Length = 45nm

---

## 🔸 10T SRAM Cell

- **Configuration**: Core 6T cell enhanced with four additional NMOS transistors to decouple the read operation  
- **Simulation Modes**:
  - `.TRAN 1ns 100ns UIC` — transient response  
  - `.DC` — DC sweep for SNM extraction  
- **Device Dimensions**:
  - **PMOS**: Width = 675nm, Length = 90nm  
  - **NMOS**: Width = 225nm, Length = 90nm  
  - **Access NMOS**: Width = 90nm, Length = 90nm

---

## 💤 Hold Mode Operation (10T Cell)

When the cell is idle (no read/write), the wordline is deactivated to isolate storage nodes:

```spice
VWL WL 0 DC=0
```

This disables access transistors, allowing internal inverters to preserve the stored state without external disturbance — a critical feature for power efficiency and data retention.

---

## 📐 Static Noise Margin (SNM) Extraction

The SNM characterizes the resilience of the cell against external noise. The butterfly curve is generated via voltage-controlled sources and a differential sweep:

```spice
EV1 V1 0 VOL='U + sqrt(2)*V(QBD)'
EV2 V2 0 VOL='-U + sqrt(2)*V(QD)'
EVD VD 0 VOL='ABS(V(V1) - V(V2))'
.DC U UL UH 0.01
.MEASURE DC SNM PARAM='1/sqrt(2)*MAX(V(VD))'
```

This approach calculates the maximum symmetrical noise margin — a critical stability metric for SRAM cells.

---

## 🔋 Dynamic Power Analysis

Average dynamic power consumption during switching operations is measured using:

```spice
.MEASURE TRAN dyn_pow AVG power FROM 0ns TO 100ns
```

This command captures the energy consumed during active memory operations.

---

## 📊 Key Observations

- ✅ Reliable data retention on Q/QB during idle (hold mode)  
- ✅ Functional write and read cycles through BL/BLB and WL pulsing  
- ✅ Static Noise Margin successfully measured via SNM butterfly plot  
- ✅ Dynamic power profile extracted and quantified  
- ✅ 10T architecture exhibits improved read stability and noise tolerance

---

## 🛠️ Simulation Requirements

- **HSPICE** or any compatible SPICE simulator (e.g., Synopsys Spectre, Ngspice)  
- **45nm PTM models** available at:  
  🔗 [http://ptm.asu.edu/](http://ptm.asu.edu/)

---

## 📚 References

- Arizona State University – *Predictive Technology Models (PTM)*: [http://ptm.asu.edu/](http://ptm.asu.edu/)  
- Weste, N. H. E., & Harris, D. (2010). *CMOS VLSI Design: A Circuits and Systems Perspective* (4th ed.).  
- Rabaey, J. M., Chandrakasan, A., & Nikolić, B. (2003). *Digital Integrated Circuits: A Design Perspective* (2nd ed.).

---

## 📜 License

This project is provided strictly for **academic and educational purposes**. Redistribution or commercial use is not permitted without explicit permission.
