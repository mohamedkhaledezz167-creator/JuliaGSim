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
* **Massive Batch Parallelism:** Evaluates hundreds of distinct circuit sizings simultaneously in memory with **up to 284× speedup** over LTspice.

---

## Verification Benchmarks ([`benchmarks/`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/tree/main/benchmarks))

👉 **[Click here to open the full `benchmarks/` directory and detailed README](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/tree/main/benchmarks)**

### Summary of Verified Topologies (GF180MCU 3.3V Thick-Oxide `nmos_3p3` / `pmos_3p3`)

| Category | Circuit Topology | Notebook Link | Batch Size | JuliaGSim Time | LTspice Time | Speedup | Key Accuracy vs. LTspice |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **DC & AC** | **5T OTA** | [`OTA_5T.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/5T_OTA/OTA_5T.ipynb) | 600 (300 DM + 300 CM) | **1.133 s** | 322.145 s | **284.33×** | Max DC diff: **4.85 mV** (`0.088 mV` at `vout`), Rank-1 `ΔAVD = 0.65 dB` |
| **DC & AC** | **Mirror OTA** | [`Mirror_OTA.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Mirror_OTA/Mirror_OTA.ipynb) | 600 (300 DM + 300 CM) | **1.867 s** | 325.206 s | **174.19×** | Max DC diff: **3.17 mV**, Rank-1 `ΔAVD = 0.07 dB`, `ΔPM = 0.57°` |
| **DC & AC** | **Miller OTA** | [`Miller_OTA.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Miller_OTA/Miller_OTA.ipynb) | 600 (300 DM + 300 CM) | **1.711 s** | 322.932 s | **188.74×** | Internal DC diff: **< 2.66 mV**, Rank-1 `ΔAVD = 0.21 dB`, `ΔPM = 0.38°` |
| **DC & AC** | **Folded Cascode OTA** | [`Folded_Cascode.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Folded_Cascode/Folded_Cascode.ipynb) | 600 (300 DM + 300 CM) | **3.653 s** | 329.670 s | **90.25×** | Max DC diff (11 nodes): **7.05 mV**, Rank-1 `ΔAVD = 1.41 dB` |
| **Transient (Step)** | **Folded Cascode Buffer** | [`Folded_Buffer_test.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Folded_Cascode/Folded_Buffer_test.ipynb) | 300 Circuits | **3.452 s** | 174.727 s | **50.62×** | Worst-Case Waveform MSE: **1.82 × 10⁻³ V²**, `ΔSR = 1.53 V/µs` |
| **Transient (Step)** | **Miller OTA Buffer** | [`Miller_Buffer_test.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Miller_OTA/Miller_Buffer_test.ipynb) | 300 Circuits | **3.843 s** | 161.375 s | **41.99×** | Mean Waveform MSE: **2.16 × 10⁻³ V²**, Mean `ΔSR = 1.05 V/µs` |
| **Transient (Sine)** | **Folded Cascode Sine** | [`Folded_Sine_test.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Folded_Cascode/Folded_Sine_test.ipynb) | 300 Circuits | **2.557 s** | 165.743 s | **64.82×** | Mean Waveform MSE: **1.46 × 10⁻⁵ V²**, Mean Gain Error: **0.40 dB** |
| **Transient (Sine)** | **Miller OTA Sine** | [`Miller_Sine_test.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Miller_OTA/Miller_Sine_test.ipynb) | 300 Circuits | **4.768 s** | 156.791 s | **32.88×** | Mean Waveform MSE: **3.31 × 10⁻⁶ V²**, Mean Gain Error: **0.47 dB** |
| **Oscillator** | **3-Stage Ring Osc** | [`Three_Stage_Ring_Osc_test.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/OSC/Three_Stage_Ring_Osc_test.ipynb) | 300 Circuits | **3.916 s** | 168.364 s | **42.99×** | Mean Freq Error: **0.54%** (Max: **1.35%**) |
| **Oscillator** | **31-Stage Ring Osc** | [`Thirty_one_Node_Ring_Osc.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/OSC/Thirty_one_Node_Ring_Osc.ipynb) | 300 Circuits | **24.442 s** | 512.693 s | **20.98×** | Mean Freq Error: **0.29%** (Max: **1.03%**) |
| **Oscillator** | **51-Stage Ring Osc** | [`Fifty_One_Node_Ring_OSC.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/OSC/Fifty_One_Node_Ring_OSC.ipynb) | 300 Circuits | **166.303 s** | 681.027 s | **4.10×** | Mean Freq Error: **1.40%** (Max: **1.97%**) |
| **Oscillator** | **Inverter LC VCO** | [`Inverter_based_LC_OSC.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/OSC/Inverter_based_LC_OSC.ipynb) | 300 Circuits | **14.643 s** | 173.452 s | **11.85×** | Mean Freq Error: **1.86%** (Max: **2.28%**, 1.42–1.85 GHz) |
| **Comparator** | **Strong-Arm Latch** | [`Strong_Arm.md`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Strong_Arm/Strong_Arm.md) / [`.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Strong_Arm/Strong_Arm_test.ipynb) | 300 Circuits | **8.545 s** | 175.280 s | **20.51×** | Mean Diff Waveform MSE: **3.78 × 10⁻² V²** |

---
