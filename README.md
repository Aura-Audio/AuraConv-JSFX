# AuraConv - JSFX

![Platform](https://img.shields.io/badge/Platform-REAPER%20JSFX-blue?style=flat-square)
![Category](https://img.shields.io/badge/Category-DSP%20Audio%20Effect-orange?style=flat-square)
![Version](https://img.shields.io/badge/Version-3.0-success?style=flat-square)
![License](https://img.shields.io/badge/License-MIT-green?style=flat-square)

**AuraConv** is a high-performance, phase-aligned moving average filter (boxcar filter) written in EEL2 for REAPER's JSFX platform. It functions as a smooth, heavy low-pass filter and transient-blurring effect, mathematically identical to convolving your audio with a uniform window. 

Built with professional DSP standards, AuraConv handles real-time parameter automation flawlessly and maintains perfect phase coherency when mixing wet and dry signals.

---

## ✨ Key Features

* **True Stereo Processing:** Independent dual-mono processing arrays maintain perfect stereo image integrity.
* **Artifact-Free Automation:** Automate the filter length in real-time without audio dropouts, pops, or zipper noise. 
* **Phase-Aligned Dry/Wet Mix:** Dry signals are dynamically extracted from the center of the delay line to perfectly align with the inherent latency of the filter, eliminating comb-filtering.
* **Automatic Plugin Delay Compensation (PDC):** Calculates exact group delay and reports it to the host DAW for perfect project synchronization.
* **Floating-Point Drift Protection:** Eliminates DC-offset accumulation inherent in continuous running-sum calculations.
* **Parameter Smoothing:** Gain and Mix parameters feature single-pole smoothing to prevent zipper artifacts.

---

## 🎛️ How to Use

| Parameter | Range | Description |
| :--- | :---: | :--- |
| **Filter Length (Taps)** | `25 - 5001` | The size of the moving average window in samples. Larger values result in a darker, more "blurred" sound, smearing transients. |
| **Output Gain** | `0.0 - 1.0` | Linear gain control applied to the wet signal to compensate for energy lost during heavy filtering. |
| **Dry/Wet Mix** | `0% - 100%` | Blends the unprocessed (but phase-aligned) signal with the filtered signal. |

*💡 **Pro Tip:** Automating the **Filter Length** via an LFO or envelope creates incredible, organic swelling effects similar to stretching the decay of a high-end reverb.*

---

## 🧠 Pro-Level DSP Architecture Under the Hood

AuraConv is built using advanced DSP optimization techniques to ensure low CPU usage and pristine audio quality. 

1. **Delay Line Architecture:** Uses a continuous, power-of-two Delay Line Array (`8192` samples). When the filter size is automated, the read pointer simply shifts back further in time. This allows for beautifully smooth, click-free manipulation of the time window.
2. **Zero-Branching Bitwise Wrapping:** Instead of using slow `if/else` conditional statements to wrap the read/write pointers, AuraConv uses bitwise AND masking (`& 8191`). This executes at the hardware level, saving crucial CPU cycles.
3. **Linear Phase Alignment:** The script forces the filter size to an **odd number**, ensuring a perfect integer group delay of exactly `(size - 1) / 2` samples. The "Dry" signal is pulled from this exact delay position, ensuring 100% phase alignment before mixing.
4. **Continuous Anti-Drift Engine:** Every time the size changes, or the buffer cycles, a highly optimized `loop()` recalculates the exact sum from scratch. This perfectly re-centers the floating-point precision, preventing DC-offset limit cycles over long sessions.

---

## 📊 Evolution: Version 2 vs. Version 3

AuraConv v3 was rebuilt from the ground up to transition from a "functional script" to a commercial-grade DSP tool. By implementing invisible safety nets, v3 protects the audio from user-generated and math-generated digital artifacts.

| Digital Artifact / Feature | Version 2 (Stable) | Version 3 (Pro-Level) | v2 Score | v3 Score |
| :--- | :--- | :--- | :---: | :---: |
| **1. Aliasing** | Math is linear; no aliasing generated. | Math is linear; no aliasing generated. | 10/10 | 10/10 |
| **2. Waveform Discontinuities** | Wipes buffer on size changes, causing massive clicks. | Delay-line architecture allows smooth size automation. | 0/10 | 9/10 |
| **3. Zipper Noise** | Stepped gain changes cause crackling. | 1-pole interpolation provides completely smooth glides. | 2/10 | 10/10 |
| **4. Phase / Comb-Filtering** | No delay compensation; ruins phase in parallel mixing. | Internal phase-alignment & DAW PDC reporting. | 1/10 | 10/10 |
| **5. Truncation Noise** | 64-bit floating point precision. | 64-bit floating point precision. | 10/10 | 10/10 |
| **6. Limit Cycles (DC Drift)** | Prevents drift on buffer wrap. | Prevents drift on wrap *and* immediately on size changes. | 8/10 | 10/10 |
| **OVERALL ARTIFACT SCORE** | **Amateur Utility** | **Commercial-Grade DSP** | **41 / 60** | **59 / 60** |

---

## 🛠️ Installation

1. Download the `AuraConv.jsfx` file.
2. Open REAPER and select **Options > Show REAPER resource path in explorer/finder**.
3. Navigate to the `Effects` folder.
4. Create a new folder named `AuraConv` and place the `AuraConv.jsfx` file inside.
5. Restart REAPER or open the FX Browser and hit **F5** to scan for new plugins.

## 📜 License & Contributing
Licensed under the MIT License. Free to use in commercial and non-commercial projects. Pull requests and DSP improvements are always welcome!
