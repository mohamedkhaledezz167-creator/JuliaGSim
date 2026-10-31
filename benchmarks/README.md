# JuliaGSim vs. LTspice Circuit Benchmarks

This directory contains the empirical verification benchmarks comparing the **JuliaGSim** vectorized in-memory circuit simulator against **LTspice** across DC operating point, AC small-signal, large-signal transient, autonomous oscillator, and regenerative latch comparator testbenches.

All benchmark circuits are simulated using the **GlobalFoundries 180nm (GF180MCU) 3.3V Thick Oxide** CMOS technology (`nmos_3p3` / `pmos_3p3` from `cmos_tt.txt`).

---

## 1. Master Benchmark Summary (All 13 Notebooks)

Every benchmark evaluates randomized batches of **300 circuits** (or **600 circuit evaluations** = 300 Differential-Mode + 300 Common-Mode for DC/AC OTA testbenches) in parallel and compares the full batch directly against LTspice.

### A. DC Operating Point & AC Small-Signal Benchmarks (600 Circuits = 300 DM + 300 CM per Topology)

In each OTA notebook, 300 randomized sizing combinations `(W, L, M_B)` are evaluated across both Differential-Mode (`sim_dm`) and Common-Mode (`sim_cm`) configurations, ranked by **worst-case DC node voltage error**, and compared across all **6 AC specifications** (`AVD`, `BW`, `UGF`, `PM`, `AVCM`, `CMRR`) alongside Bode Magnitude and Phase plots for the top 10 worst-case circuits:

| Topology & Notebook | Batch Size | JuliaGSim Time | LTspice Time | Speedup | Worst-Case DC Error (Rank 1 / 300) | Rank 1 AC Metrics Discrepancy (JuliaGSim vs. LTspice) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **[5T OTA](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/5T_OTA/OTA_5T.ipynb)** | 600 (300 DM + 300 CM) | **1.133 s** | 322.145 s | **284.33×** | **4.847 mV** (`vT`)<br>**0.088 mV** (`vout`) | `ΔAVD` = 0.65 dB, `ΔBW` = 14.05%, `ΔUGF` = 20.02%, `ΔPM` = 1.74°, `ΔAVCM` = 1.48 dB, `ΔCMRR` = 0.83 dB |
| **[Mirror OTA](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Mirror_OTA/Mirror_OTA.ipynb)** | 600 (300 DM + 300 CM) | **1.867 s** | 325.206 s | **174.19×** | **3.166 mV** (`vout`) | `ΔAVD` = 0.07 dB, `ΔBW` = 1.00%, `ΔUGF` = 0.35%, `ΔPM` = 0.57°, `ΔAVCM` = 0.04 dB, `ΔCMRR` = 0.10 dB |
| **[Miller OTA](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Miller_OTA/Miller_OTA.ipynb)** | 600 (300 DM + 300 CM) | **1.711 s** | 322.932 s | **188.74×** | **30.284 mV** (`vout`, 95 dB open-loop)<br>**< 2.662 mV** (internal nodes) | `ΔAVD` = 0.21 dB, `ΔBW` = 4.01%, `ΔUGF` = 0.69%, `ΔPM` = 0.38°, `ΔAVCM` = 0.04 dB, `ΔCMRR` = 0.25 dB |
| **[Folded Cascode OTA](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Folded_Cascode/Folded_Cascode.ipynb)** | 600 (300 DM + 300 CM) | **3.653 s** | 329.670 s | **90.25×** | **7.050 mV** (`vout`) | `ΔAVD` = 1.41 dB, `ΔBW` = 1.15%, `ΔUGF` = 17.04%, `ΔPM` = 2.35°, `ΔAVCM` = 0.86 dB, `ΔCMRR` = 2.27 dB |

---

### B. Large-Signal Amplifier Transient Benchmarks (300 Circuits per Testbench)

| Topology & Notebook | Testbench Type | JuliaGSim Time | LTspice Time | Speedup | Measured Metrics | Measured Accuracy Across 300 Circuits |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **[Folded Cascode Buffer](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Folded_Cascode/Folded_Buffer_test.ipynb)** | Unity-Gain Step Response (1.2 V to 1.8 V) | **3.452 s** | 174.727 s | **50.62×** | Waveform MSE (`V²`),<br>Slew Rate (`V/µs`) | Worst-Case MSE: **1.8235 × 10⁻³ V²**<br>Worst-Case `ΔSR`: **1.53 V/µs** (20.90 vs. 19.37 V/µs) |
| **[Miller OTA Buffer](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Miller_OTA/Miller_Buffer_test.ipynb)** | Unity-Gain Step Response (1.2 V to 1.8 V) | **3.843 s** | 161.375 s | **41.99×** | Waveform MSE (`V²`),<br>Slew Rate (`V/µs`) | Mean MSE: **2.1567 × 10⁻³ V²** (Max: 9.0463 × 10⁻³ V²)<br>Mean `ΔSR`: **1.05 V/µs** (Max: 5.58 V/µs) |
| **[Folded Cascode Sine](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Folded_Cascode/Folded_Sine_test.ipynb)** | Open-Loop 1 kHz Sine (`0.5 mV` peak) | **2.557 s** | 165.743 s | **64.82×** | Waveform MSE (`V²`),<br>Transient Gain (`V/V` & `dB`) | Mean MSE: **1.4649 × 10⁻⁵ V²** (Max: 1.6162 × 10⁻⁴ V²)<br>Mean Gain Error: **0.40 dB** (Max: 1.92 dB) |
| **[Miller OTA Sine](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Miller_OTA/Miller_Sine_test.ipynb)** | Open-Loop 10 Hz Sine (`20 µV` peak) | **4.768 s** | 156.791 s | **32.88×** | Waveform MSE (`V²`),<br>Transient Gain (`V/V` & `dB`) | Mean MSE: **3.3112 × 10⁻⁶ V²** (Max: 1.0120 × 10⁻⁴ V²)<br>Mean Gain Error: **0.47 dB** (Max: 0.90 dB) |

---

### C. Autonomous Oscillator & Clocked Comparator Transient Benchmarks (300 Circuits per Testbench)

| Topology & Notebook | Sweep Parameter (300 Circuits) | JuliaGSim Time | LTspice Time | Speedup | Measured Metrics | Measured Accuracy Across 300 Circuits |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **[3-Stage Ring Oscillator](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/OSC/Three_Stage_Ring_Osc_test.ipynb)** | Load Capacitance `C_L` (36.7 – 176.8 MHz) | **3.916 s** | 168.364 s | **42.99×** | Steady-State Oscillation Frequency (`MHz`) & Nodal Waveforms | Mean Freq Error: **0.54%**<br>Max Freq Error: **1.35%** |
| **[31-Stage Ring Oscillator](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/OSC/Thirty_one_Node_Ring_Osc.ipynb)** | Load Capacitance `C_L` (Static & Sparse Solvers) | **24.442 s** (Static)<br>**62.244 s** (Sparse) | 512.693 s<br>516.608 s | **20.98×**<br>**8.30×** | Steady-State Oscillation Frequency (`MHz`) & Nodal Waveforms | Mean Freq Error: **0.29%**<br>Max Freq Error: **1.03%** |
| **[51-Stage Ring Oscillator](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/OSC/Fifty_One_Node_Ring_OSC.ipynb)** | Load Capacitance `C_L` (Sparse + Gear BDF2) | **166.303 s** | 681.027 s | **4.10×** | Steady-State Oscillation Frequency (`MHz`) & Nodal Waveforms | Mean Freq Error: **1.40%**<br>Max Freq Error: **1.97%** |
| **[Inverter-Based LC Oscillator](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/OSC/Inverter_based_LC_OSC.ipynb)** | Tank Capacitance `C_L` (`1.0 – 2.0 pF`, 1.42 – 1.85 GHz) | **14.643 s** | 173.452 s | **11.85×** | Steady-State Oscillation Frequency (`MHz`) & Tank Waveforms (`VX`, `VY`) | Mean Freq Error (Gear BDF2): **1.86%**<br>Max Freq Error: **2.28%** |
| **[Strong-Arm Latch Comparator](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Strong_Arm/Strong_Arm.md)** | Device Sizing `W, L` (`50 MHz` Clock, `ΔVin = 20 mV`) | **8.545 s** | 175.280 s | **20.51×** | Differential (`Vop - Von`) & Single-Ended (`Vop`, `Von`) Waveform MSE (`V²`) | Mean Diff MSE: **3.7801 × 10⁻² V²** (Max: 4.3864 × 10⁻² V²)<br>Mean `Vop` MSE: **3.4211 × 10⁻² V²**<br>Mean `Von` MSE: **2.7458 × 10⁻² V²** |

---

## 2. Detailed Benchmark Directory Breakdown

### 1. Five-Transistor Operational Transconductance Amplifier (`5T_OTA/`)
* **[`OTA_5T.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/5T_OTA/OTA_5T.ipynb)**
  * **Circuit Under Test:** NMOS-input 5-transistor OTA with diode-connected bias mirror (`VDD = 3.0 V`, `VCM = 1.5 V`, `IB = 50 µA`, `CL = 1 pF`).
  * **What is Measured:**
    * Parallel simulation of **600 circuits** (300 Differential-Mode + 300 Common-Mode) across randomized transistor widths `W`, lengths `L`, and bias multipliers `M_B`.
    * **DC Operating Point:** Nodal voltages at `vb`, `vm`, `vT`, and `vout`. Worst-case error across all 300 circuits is **4.847 mV** on `vT` and **0.088 mV** on `vout`.
    * **AC Small-Signal:** Full extraction and comparison of `AVD (dB)`, `BW (Hz)`, `UGF (Hz)`, `PM (deg)`, `AVCM (dB)`, and `CMRR (dB)`, plus overlaid Bode Magnitude and Phase plots for the 10 worst-case circuits.
    * **Runtime:** `1.133 s` (JuliaGSim) vs. `322.145 s` (LTspice) — **284.33× speedup**.

---

### 2. Folded-Cascode Operational Transconductance Amplifier (`Folded_Cascode/`)
* **[`Folded_Cascode.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Folded_Cascode/Folded_Cascode.ipynb)** — *DC Operating Point & AC Small-Signal Verification*
  * **Circuit Under Test:** NMOS-input Folded-Cascode OTA (`VDD = 3.0 V`, `VCM = 1.5 V`, `CL = 1 pF`).
  * **What is Measured:**
    * Parallel evaluation of **600 circuits** (300 DM + 300 CM).
    * **DC Operating Point:** All **11 internal and output nodes** (`vb`, `Vbp`, `VcscP`, `VcscN`, `vT`, `vfold_p`, `vfold_n`, `vx_left`, `vy_left`, `vout_bot`, `vout`). Worst-case DC error across all 300 designs is **7.050 mV**.
    * **AC Small-Signal:** `AVD (dB)`, `BW (Hz)`, `UGF (Hz)`, `PM (deg)`, `AVCM (dB)`, and `CMRR (dB)`, with Bode Magnitude and Phase overlays for the top 10 worst-case circuits.
    * **Runtime:** `3.653 s` (JuliaGSim) vs. `329.670 s` (LTspice) — **90.25× speedup**.
* **[`Folded_Buffer_test.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Folded_Cascode/Folded_Buffer_test.ipynb)** — *Unity-Gain Buffer Large-Signal Step Response*
  * **What is Measured:** Large-signal step response (`1.2 V` to `1.8 V` input pulse with `10 ns` rise/fall times over `0.5 µs`) in unity-gain feedback across 8-circuit, 100-circuit, and **300-circuit** batches. Extracts full-waveform **Mean Squared Error (`MSE` in `V²`)** and **Slew Rate (`V/µs`)**, ranking circuits from worst to best MSE.
  * **Runtime (300 Circuits):** `3.452 s` (JuliaGSim) vs. `174.727 s` (LTspice) — **50.62× speedup**.
* **[`Folded_Sine_test.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Folded_Cascode/Folded_Sine_test.ipynb)** — *Open-Loop Sinusoidal Transient Response*
  * **What is Measured:** Open-loop transient amplification of a `1 kHz`, `0.5 mV` amplitude sine wave over `2 ms` across **300 circuits**. Extracts output **Waveform MSE (`V²`)**, **Transient Linear Gain (`V/V`)**, and **Gain Discrepancy (`dB`)** against LTspice (`Mean Gain Error = 0.40 dB`, `Mean MSE = 1.4649 × 10⁻⁵ V²`).
  * **Runtime (300 Circuits):** `2.557 s` (JuliaGSim) vs. `165.743 s` (LTspice) — **64.82× speedup**.

---

### 3. Two-Stage Miller-Compensated OTA (`Miller_OTA/`)
* **[`Miller_OTA.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Miller_OTA/Miller_OTA.ipynb)** — *DC Operating Point & AC Small-Signal Verification*
  * **Circuit Under Test:** PMOS-input two-stage Miller-compensated OTA with nulling resistor `Rz`, compensation capacitor `Cc`, and load capacitor `CL` (`VDD = 3.0 V`, `VCM = 1.5 V`).
  * **What is Measured:**
    * Parallel evaluation of **600 circuits** (300 DM + 300 CM).
    * **DC Operating Point:** Nodal voltages at `vb`, `tail`, `v1`, `vx`, and `vout`. Internal nodes match within **2.662 mV**, and the unbuffered high-gain (`~95 dB`) output node `vout` has a worst-case discrepancy of **30.284 mV** across all 300 circuits.
    * **AC Small-Signal:** `AVD (dB)`, `BW (Hz)`, `UGF (Hz)`, `PM (deg)`, `AVCM (dB)`, and `CMRR (dB)`, plus Bode Magnitude and Phase plots for the 10 worst-case circuits.
    * **Runtime:** `1.711 s` (JuliaGSim) vs. `322.932 s` (LTspice) — **188.74× speedup**.
* **[`Miller_Buffer_test.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Miller_OTA/Miller_Buffer_test.ipynb)** — *Unity-Gain Buffer Large-Signal Step Response*
  * **What is Measured:** Large-signal step response (`1.2 V` to `1.8 V`) across **300 circuits**, measuring **Waveform MSE (`V²`)** (`Mean = 2.1567 × 10⁻³ V²`) and **Slew Rate (`V/µs`)** (`Mean Error = 1.05 V/µs`).
  * **Runtime (300 Circuits):** `3.843 s` (JuliaGSim) vs. `161.375 s` (LTspice) — **41.99× speedup**.
* **[`Miller_Sine_test.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Miller_OTA/Miller_Sine_test.ipynb)** — *Open-Loop Sinusoidal Transient Response*
  * **What is Measured:** Open-loop transient amplification of a `10 Hz`, `20 µV` amplitude sine wave over `0.2 s` across **300 circuits**. Measures **Waveform MSE (`V²`)** (`Mean = 3.3112 × 10⁻⁶ V²`), **Transient Linear Gain (`V/V`)**, and **Gain Discrepancy (`dB`)** (`Mean = 0.47 dB`, `Max = 0.90 dB`).
  * **Runtime (300 Circuits):** `4.768 s` (JuliaGSim) vs. `156.791 s` (LTspice) — **32.88× speedup**.

---

### 4. Symmetric Current-Mirror OTA (`Mirror_OTA/`)
* **[`Mirror_OTA.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Mirror_OTA/Mirror_OTA.ipynb)** — *DC Operating Point & AC Small-Signal Verification*
  * **Circuit Under Test:** NMOS-input symmetric Current-Mirror OTA (`VDD = 3.0 V`, `VCM = 1.5 V`, `CL = 1 pF`).
  * **What is Measured:**
    * Parallel evaluation of **600 circuits** (300 DM + 300 CM).
    * **DC Operating Point:** Nodal voltages at `vb_n`, `vT`, `v_diode1`, `v_diode2`, `v_mir1`, and `vout`. Worst-case DC error across all 300 circuits is **3.166 mV**.
    * **AC Small-Signal:** `AVD (dB)`, `BW (Hz)`, `UGF (Hz)`, `PM (deg)`, `AVCM (dB)`, and `CMRR (dB)`, plus Bode Magnitude and Phase plots for the top 10 worst-case circuits.
    * **Runtime:** `1.867 s` (JuliaGSim) vs. `325.206 s` (LTspice) — **174.19× speedup**.

---

### 5. Autonomous Ring & LC Oscillators (`OSC/`)
* **[`Three_Stage_Ring_Osc_test.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/OSC/Three_Stage_Ring_Osc_test.ipynb)** — *3-Stage CMOS Ring Oscillator*
  * **What is Measured:** Transient startup and steady-state oscillation across **300 circuits** sweeping load capacitance `C_L`. Measures steady-state **Oscillation Frequency (`MHz`)** via `1.5 V` rising-edge crossings (`36.74 MHz` to `176.82 MHz`) and 3-phase waveforms (`VA`, `VB`, `VC`).
  * **Accuracy & Runtime:** Mean frequency error of **0.54%** (Max **1.35%**); `3.916 s` vs. `168.364 s` (**42.99× speedup**).
* **[`Thirty_one_Node_Ring_Osc.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/OSC/Thirty_one_Node_Ring_Osc.ipynb)** — *31-Stage CMOS Ring Oscillator*
  * **What is Measured:** 62-transistor, 31-node ring oscillator evaluated across **300 circuits** using both **Static** and **Sparse** solver engines. Extracts steady-state **Oscillation Frequency (`MHz`)** and multi-node transient waveforms (`V1`, `V11`, `V21`).
  * **Accuracy & Runtime:** Mean frequency error of **0.29%** (Max **1.03%**); Static solver runs in `24.442 s` vs. `512.693 s` (**20.98× speedup**), Sparse solver runs in `62.244 s` vs. `516.608 s` (**8.30× speedup**).
* **[`Fifty_One_Node_Ring_OSC.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/OSC/Fifty_One_Node_Ring_OSC.ipynb)** — *51-Stage CMOS Ring Oscillator*
  * **What is Measured:** 102-transistor, 51-node ring oscillator evaluated across **300 circuits** using **Sparse Arrays** and the **Gear (BDF2)** integration method (`0` to `400 ns`). Extracts steady-state **Oscillation Frequency (`MHz`)** (`10.26 MHz` to `19.51 MHz`) and phase waveforms (`V1`, `V17`, `V34`).
  * **Accuracy & Runtime:** Mean frequency error of **1.40%** (Max **1.97%**); `166.303 s` vs. `681.027 s` (**4.10× speedup**).
* **[`Inverter_based_LC_OSC.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/OSC/Inverter_based_LC_OSC.ipynb)** — *Cross-Coupled Complementary CMOS LC Oscillator*
  * **What is Measured:** High-Q complementary cross-coupled LC tank oscillator (`L = 10 nH` with `R_ser = 2 Ω`, sweeping tank capacitance `C_L` from `1.0 pF` to `2.0 pF` across **300 circuits**). Compares both **Trapezoidal** and **Gear (BDF2)** integration methods against LTspice (`1418.93 MHz` to `1852.60 MHz`) and plots steady-state differential tank waveforms (`VX`, `VY`).
  * **Accuracy & Runtime:** Mean frequency error of **1.86%** (Max **2.28%**) with Gear BDF2; `14.643 s` vs. `173.452 s` (**11.85× speedup**).

---

### 6. Regenerative Clocked Comparator (`Strong_Arm/`)
* **[`Strong_Arm.md`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Strong_Arm/Strong_Arm.md)** / **[`Strong_Arm_test.ipynb`](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Strong_Arm/Strong_Arm_test.ipynb)**
  * **Circuit Under Test:** Clocked Strong-Arm regenerative latch comparator (`VDD = 3.0 V`, `VCM = 1.5 V`, `ΔVin = 20 mV` differential input, `50 MHz` clock over `45 ns`) evaluated across **300 randomized transistor sizing combinations (`W, L`)**.
  * **What is Measured:**
    * **Differential Output Waveform MSE (`MSE_Diff` in `V²`):** Compares `V(vop) - V(von)` across the entire multi-cycle precharge and regeneration transient (`Mean = 3.7801 × 10⁻² V²`, `Max = 4.3864 × 10⁻² V²`).
    * **Single-Ended Output Waveform MSE (`MSE_Vop`, `MSE_Von` in `V²`):** `Mean MSE_Vop = 3.4211 × 10⁻² V²`, `Mean MSE_Von = 2.7458 × 10⁻² V²`.
    * **4-Pane Transient Waveform Overlays:** `V(clk)`, single-ended `V(vop)`, single-ended `V(von)`, and differential `V(vop) - V(von)` for the top 10 worst-case circuits.
    * **Runtime:** `8.545 s` (JuliaGSim) vs. `175.280 s` (LTspice) — **20.51× speedup**.
