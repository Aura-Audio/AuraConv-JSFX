# AuraConv-JSFX

## AuraConv Filter 🌊

![Platform](https://img.shields.io/badge/Platform-REAPER%20JSFX-blue?style=flat-square)
![Category](https://img.shields.io/badge/Category-DSP%20Audio%20Effect-orange?style=flat-square)
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

## 🛠️ Installation

1. Download the `AuraConv.jsfx` file.
2. Open REAPER and select **Options > Show REAPER resource path in explorer/finder**.
3. Navigate to the `Effects` folder.
4. Create a new folder named `AuraConv` (optional, for organization) and place the `AuraConv.jsfx` file inside.
5. Restart REAPER or open the FX Browser and hit **F5** to scan for new plugins.
6. Search for `AuraConv` in your FX list!

---

## 🎛️ How to Use

| Parameter | Range | Description |
| :--- | :---: | :--- |
| **Filter Length (Taps)** | `25 - 5001` | The size of the moving average window in samples. Larger values result in a darker, more "blurred" sound, smearing transients and removing high frequencies. |
| **Output Gain** | `0.0 - 1.0` | Linear gain control applied to the wet signal to compensate for energy lost during heavy filtering. |
| **Dry/Wet Mix** | `0% - 100%` | Blends the unprocessed (but phase-aligned) signal with the filtered signal. |

*💡 **Pro Tip:** Automating the **Filter Length** via an LFO or envelope creates incredible, organic swelling effects similar to stretching the decay of a high-end reverb.*

---

## 🧠 Pro-Level DSP Architecture Under the Hood

AuraConv is built using advanced DSP optimization techniques to ensure low CPU usage and pristine audio quality. 

### 1. Delay Line Architecture vs. Basic Buffers
Naive moving average filters often use a circular buffer that must be cleared when the filter size changes, causing audio dropouts. AuraConv utilizes a continuous, power-of-two **Delay Line Array** (`8192` samples). When the filter size is automated, the read pointer simply shifts back further in time. This allows for beautifully smooth, click-free manipulation of the time window.

### 2. Zero-Branching Bitwise Buffer Wrapping
Instead of using slow `if/else` conditional statements to wrap the read/write pointers at the end of the buffer (e.g., `if (pos >= MAX_SIZE) pos = 0;`), AuraConv uses bitwise AND masking (`& 8191`). This executes at the hardware level, saving crucial CPU cycles per sample.

### 3. Linear Phase Alignment & Constant Group Delay
A moving average (FIR) filter introduces a delay proportional to its size. Mixing a delayed wet signal with an undelayed dry signal creates severe comb filtering. 
AuraConv solves this mathematically:
* It forces the filter size to always be an **odd number**.
* This guarantees a perfect integer group delay of precisely `(size - 1) / 2` samples.
* The script pulls the "Dry" signal from exactly `(size - 1) / 2` samples back in the delay line, ensuring the Dry and Wet signals are 100% phase-aligned before mixing.

### 4. Continuous Anti-Drift Mechanism
Standard optimized moving averages calculate the sum by subtracting the oldest sample and adding the newest (`sum = sum - old + new`). Over millions of samples, microscopic floating-point rounding errors accumulate, causing the center of the waveform to drift (DC offset), eventually destroying headroom. 
AuraConv features an invisible **Anti-Drift Engine**. Every time the size changes, or the buffer cycles, a highly optimized `loop()` recalculates the exact sum from scratch. In EEL2, this takes less than a microsecond and perfectly re-centers the floating-point precision, ensuring infinite stability during long performances.

---

## 📜 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details. Free to use in commercial and non-commercial projects.

## 🤝 Contributing

Pull requests and DSP improvements are always welcome! If you find a bug or have a feature request, please open an issue.
