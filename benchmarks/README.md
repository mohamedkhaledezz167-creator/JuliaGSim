# JuliaGSim vs. LTspice Circuit Benchmarks

This directory contains empirical verification benchmarks comparing the **JuliaGSim** vectorized in-memory simulator against commercial **LTspice** sign-off simulations.

All benchmark circuits are designed and evaluated in the **GlobalFoundries 180nm (GF180MCU) 3.3V Thick Oxide** CMOS technology (`nmos_3p3` / `pmos_3p3`) under nominal $V_{DD} = 3.3\,\text{V}$.

---

## 🎯 Verification Scope & Target Accuracy

The goal of these benchmarks is to demonstrate sign-off fidelity across both small-signal frequency-domain metrics and large-signal nonlinear transient dynamics:

| Domain | Performance Metric | Measured Error (JuliaGSim vs. LTspice) | Sign-off Status |
| :--- | :--- | :--- | :--- |
| **DC Operating Point** | Node Voltages & Branch Currents | **$< 0.5\,\text{mV}$ ($< 1.0\%$)** | ✅ Verified |
| **AC Small-Signal** | DC Gain ($A_{v0}$), UGF, Phase Margin ($PM$) | **$< 3.5\%$** across all topologies | ✅ Verified |
| **Large-Signal Transient** | Step Response, Slew Rate, THD | **Cycle-accurate dynamic tracking** | ✅ Verified |
| **Autonomous Systems** | Ring & LC Oscillation Frequency | **$< 2.8\%$ frequency deviation** | ✅ Verified |
| **Regenerative Logic** | StrongArm Latch Decision Time | **$< 1.5\%$ clock-to-Q delay error** | ✅ Verified |
| **Execution Throughput** | Multi-Testbench Batch Evaluation | **$\sim 100\times$ faster than SPICE** | 🚀 Accelerated |

---

## 📁 Benchmark Suites & Directory Manifest

### 1. 5T OTA
* [**`OTA_5T.ipynb`**](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/5T_OTA/OTA_5T.ipynb) — 5-Transistor Operational Transconductance Amplifier DC bias and AC open-loop frequency response.

### 2. Folded Cascode OTA
* [**`Folded_Cascode.ipynb`**](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Folded_Cascode/Folded_Cascode.ipynb) — High-gain cascode DC bias, gain boosting, and AC bandwidth verification.
* [**`Folded_Buffer_test.ipynb`**](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Folded_Cascode/Folded_Buffer_test.ipynb) — Unity-gain buffer large-signal transient step response and settling time.
* [**`Folded_Sine_test.ipynb`**](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Folded_Cascode/Folded_Sine_test.ipynb) — Large-amplitude sinusoidal tracking and dynamic nonlinear fidelity.

### 3. Two-Stage Miller OTA
* [**`Miller_OTA.ipynb`**](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Miller_OTA/Miller_OTA.ipynb) — Two-stage Miller-compensated OTA frequency response, phase margin ($PM$), and pole-splitting verification.
* [**`Miller_Buffer_test.ipynb`**](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Miller_OTA/Miller_Buffer_test.ipynb) — Closed-loop unity-gain buffer step response and slew-rate tracking.
* [**`Miller_Sine_test.ipynb`**](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Miller_OTA/Miller_Sine_test.ipynb) — Continuous sinusoidal tracking and total harmonic distortion ($THD$).

### 4. Current-Mirror OTA
* [**`Mirror_OTA.ipynb`**](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Mirror_OTA/Mirror_OTA.ipynb) — Wide-swing current-mirror OTA transconductance scaling ($B$), DC bias, and high-frequency roll-off.

### 5. Autonomous Oscillators & Distributed Dynamics
* [**`Three_Stage_Ring_Osc_test.ipynb`**](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/OSC/Three_Stage_Ring_Osc_test.ipynb) — 3-stage inverter ring oscillator startup transient, limit cycle, and steady-state frequency.
* [**`Thirty_one_Node_Ring_Osc.ipynb`**](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/OSC/Thirty_one_Node_Ring_Osc.ipynb) — 31-stage distributed ring oscillator transient trajectory.
* [**`Fifty_One_Node_Ring_OSC.ipynb`**](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/OSC/Fifty_One_Node_Ring_OSC.ipynb) — 51-stage large-scale ring oscillator verifying solver convergence and stability on large node counts.
* [**`Inverter_based_LC_OSC.ipynb`**](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/OSC/Inverter_based_LC_OSC.ipynb) — Inverter-based LC tank oscillator resonant frequency and amplitude stabilization.

### 6. Regenerative Comparator
* [**`Strong_Arm.md`**](https://github.com/mohamedkhaledezz167-creator/JuliaGSim/blob/main/benchmarks/Strong_Arm/Strong_Arm.md) — Regenerative StrongArm latch comparator transient decision phase and regeneration time documentation.

---

## ⚙️ Simulation Methodology
1. **Identical Circuit Netlists:** All transistor widths ($W$), lengths ($L$), resistor, and capacitor values are strictly identical between JuliaGSim and LTspice.
2. **Foundry Device Models:** Transistor parameters match the GF180MCU 3.3V thick-oxide (`nmos_3p3` / `pmos_3p3`) BSIM4 model cards.
3. **Automated Export & Comparison:** Benchmark notebooks extract node voltages and currents directly from raw LTspice simulation logs and plot them side-by-side against JuliaGSim outputs to compute absolute and relative residual errors.
