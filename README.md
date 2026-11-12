# JuliaGSim
JuliaGSim: Vectorized in-memory analog circuit simulator powered by tensor MOS surrogate models. Validated against commercial SPICE (LTspice) across DC, AC, and large-signal transient benchmarks . All Rights Reserved.



# Intellectual Property & Trademark Notice:
The name JuliaGSim™, its architecture (VSIM / VOPT), and its mathematical circuit description syntax (Circuit, add_mos!, compile!) are the exclusive intellectual property of Mohamed Khaled Ezzeldin. Copying the API syntax or using the JuliaGSim name to endorse or name any other software is strictly prohibited.


# JuliaGSim™

**JuliaGSim** is a high-throughput, vectorized in-memory analog circuit simulator (`VSIM`) designed for closed-loop analog design automation (`VOPT`). The initial simulator architecture and codebase were originally developed in **PyTorch** by **Mohamed Khaled Ezzeldin**, and subsequently translated into **Julia** with **Gemini 3.1 Pro (Google Antigravity)**.

Validated against commercial SPICE (**LTspice**) across **DC Operating Point**, **AC Small-Signal**, **Large-Signal Amplifier Transient**, **Autonomous Ring & LC Oscillator**, and **Regenerative Clocked Comparator** benchmarks in the **GlobalFoundries 180nm (GF180MCU) 3.3V Thick Oxide** CMOS technology (`nmos_3p3` / `pmos_3p3`).

---

## Key Capabilities

* **Multi-Domain Circuit Simulation:**
  * **DC Operating Point:** Full nodal voltage and branch operating point evaluation.
  * **AC Small-Signal:** Frequency-domain response extracting `AVD`, `BW`, `UGF`, `PM`, `AVCM`, and `CMRR`.
  * **Large-Signal Transient:** Time-domain simulation for nonlinear amplifiers, autonomous oscillators, and clocked regenerative circuits.
* **Massive Batch Parallelism:** Evaluates hundreds of distinct circuit sizings simultaneously in memory with **up to 96× speedup** over LTspice.

---

## Verification Benchmarks ([`benchmarks/`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/tree/main/benchmarks))

👉 **[Click here to open the full `benchmarks/` directory and detailed README](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/tree/main/benchmarks)**

### Summary of Verified Topologies (GF180MCU 3.3V Thick-Oxide `nmos_3p3` / `pmos_3p3`)

| Category | Circuit Topology | Notebook Link | Batch Size | JuliaGSim Time | LTspice Time | Speedup | Key Accuracy vs. LTspice |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **DC & AC** | **5T OTA** | [`OTA_5T.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/5T_OTA/OTA_5T.ipynb) | 600 (300 DM + 300 CM) | **3.154 s** | 304.061 s | **96.40×** | Max DC diff: **4.85 mV** (`0.088 mV` at `vout`), Rank-1 `ΔAVD = 0.65 dB` |
| **DC & AC** | **Mirror OTA** | [`Mirror_OTA.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Mirror_OTA/Mirror_OTA.ipynb) | 600 (300 DM + 300 CM) | **4.470 s** | 305.810 s | **68.41×** | Max DC diff: **3.17 mV**, Rank-1 `ΔAVD = 0.07 dB`, `ΔPM = 0.95°` |
| **DC & AC** | **Miller OTA** | [`Miller_OTA.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Miller_OTA/Miller_OTA.ipynb) | 600 (300 DM + 300 CM) | **4.162 s** | 310.510 s | **74.61×** | Internal DC diff: **< 2.70 mV**, Rank-1 `ΔAVD = 0.21 dB`, `ΔPM = 1.87°` |
| **DC & AC** | **Folded Cascode OTA** | [`Folded_Cascode.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Folded_Cascode/Folded_Cascode.ipynb) | 600 (300 DM + 300 CM) | **8.153 s** | 311.392 s | **38.19×** | Max DC diff (11 nodes): **7.05 mV**, Rank-1 `ΔAVD = 1.41 dB` |
| **Transient (Step)** | **Folded Cascode Buffer** | [`Folded_Buffer_test.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Folded_Cascode/Folded_Buffer_test.ipynb) | 300 Circuits | **2.560 s** | 166.102 s | **64.88×** | Worst-Case Waveform MSE: **5.93 × 10⁻⁴ V²**, `ΔSR = 0.27 V/µs` |
| **Transient (Step)** | **Miller OTA Buffer** | [`Miller_Buffer_test.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Miller_OTA/Miller_Buffer_test.ipynb) | 300 Circuits | **3.851 s** | 161.075 s | **41.83×** | Mean Waveform MSE: **1.33 × 10⁻³ V²**, Mean `ΔSR = 0.89 V/µs` |
| **Transient (Sine)** | **Folded Cascode Sine** | [`Folded_Sine_test.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Folded_Cascode/Folded_Sine_test.ipynb) | 300 Circuits | **5.407 s** | 167.266 s | **30.94×** | Mean Waveform MSE: **1.14 × 10⁻⁵ V²**, Mean Gain Error: **0.34 dB** |
| **Transient (Sine)** | **Miller OTA Sine** | [`Miller_Sine_test.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Miller_OTA/Miller_Sine_test.ipynb) | 300 Circuits | **7.088 s** | 157.440 s | **22.21×** | Mean Waveform MSE: **3.19 × 10⁻⁶ V²**, Mean Gain Error: **0.46 dB** |
| **Oscillator** | **3-Stage Ring Osc** | [`Three_Stage_Ring_Osc_test.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/OSC/Three_Stage_Ring_Osc_test.ipynb) | 300 Circuits | **3.231 s** | 167.840 s | **51.95×** | Mean Freq Error: **0.13%** (Max: **0.34%**) |
| **Oscillator** | **31-Stage Ring Osc** | [`Thirty_one_Node_Ring_Osc.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/OSC/Thirty_one_Node_Ring_Osc.ipynb) | 300 Circuits | **24.587 s** | 499.447 s | **20.31×** | Mean Freq Error: **0.00%** (Max: **0.01%**) |
| **Oscillator** | **51-Stage Ring Osc** | [`Fifty_One_Node_Ring_OSC.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/OSC/Fifty_One_Node_Ring_OSC.ipynb) | 300 Circuits | **167.080 s** | 670.267 s | **4.01×** | Mean Freq Error: **0.02%** (Max: **0.03%**) |
| **Oscillator** | **Inverter LC VCO** | [`Inverter_based_LC_OSC.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/OSC/Inverter_based_LC_OSC.ipynb) | 300 Circuits | **8.983 s** | 172.479 s | **19.20×** | Mean Freq Error: **0.08%** (Max: **0.12%**, 1.40–1.81 GHz) |
| **Comparator** | **Strong-Arm Latch** | [`Strong_Arm.md`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Strong_Arm/Strong_Arm.md) / [`.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Strong_Arm/Strong_Arm_test.ipynb) | 300 Circuits | **8.540 s** | 177.577 s | **20.79×** | Mean Diff Waveform MSE: **1.06 × 10⁻⁴ V²** (Max: **1.19 × 10⁻⁴ V²**) |

# Intellectual Property & Trademark Notice:
The name JuliaGSim™, its architecture (VSIM / VOPT), and its mathematical circuit description syntax (Circuit, add_mos!, compile!) are the exclusive intellectual property of Mohamed Khaled Ezzeldin. Copying the API syntax or using the JuliaGSim name to endorse or name any other software is strictly prohibited.
