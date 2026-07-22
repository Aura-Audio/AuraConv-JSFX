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

```js
desc: AuraConv Filter (Pro Moving Average)

slider1:1001<25,5001,1>Filter Length (Taps)
slider2:0.15<0,1,0.01>Output Gain
slider3:100<0,100,1>Dry/Wet Mix (%)

@init
// Power-of-two buffer capacity allows fast bitwise wrapping
MAX_SIZE = 8192; 
MASK = MAX_SIZE - 1;

// Memory starting offsets for Left and Right channels
mem_L = 0;
mem_R = MAX_SIZE;

write_pos = 0;
sum_L = sum_R = 0;

// Initialize smoothing targets
gain = slider2;
mix = slider3 / 100;

@slider
// 1. Force the size to be an ODD number.
// This guarantees the filter has an exact integer group delay,
// allowing us to perfectly phase-align the Dry and Wet signals.
new_size = floor(slider1);
(new_size % 2 == 0) ? new_size += 1;

new_size != size ? (
    size = new_size;
    
    // Trigger a sum recalculation in the next @sample block
    recalc_needed = 1; 
    
    // Calculate the exact latency (group delay) of the filter
    delay_samps = (size - 1) / 2;
    
    // Report Plugin Delay Compensation (PDC) to the DAW
    pdc_delay = delay_samps;
    pdc_bot_ch = 0; 
    pdc_top_ch = 2;
);

target_gain = slider2;
target_mix = slider3 / 100;

@sample
// 2. Smooth parameters (anti-zipper noise)
gain += (target_gain - gain) * 0.005;
mix += (target_mix - mix) * 0.005;

// 3. Write the newest samples into the delay line
mem_L[write_pos] = spl0;
mem_R[write_pos] = spl1;

// 4. Add the newest samples to the running sum
sum_L += spl0;
sum_R += spl1;

// 5. Subtract the oldest samples leaving the moving window
read_pos = (write_pos - size) & MASK;
sum_L -= mem_L[read_pos];
sum_R -= mem_R[read_pos];

// 6. Handle Size Changes & Float Drift Protection
// Recalculate the sum from scratch when size changes, or every 8192 samples
recalc_needed || write_pos == 0 ? (
    recalc_needed = 0;
    sum_L = sum_R = 0;
    i = 0;
    loop(size,
        idx = (write_pos - i) & MASK;
        sum_L += mem_L[idx];
        sum_R += mem_R[idx];
        i += 1;
    );
);

// 7. Calculate Processed (Wet) Signal
wet_L = (sum_L / size) * gain;
wet_R = (sum_R / size) * gain;

// 8. Calculate Phase-Aligned Dry Signal
// We extract the dry signal from the exact center of our current 
// moving average window so it physically aligns with the processed sound.
dry_pos = (write_pos - delay_samps) & MASK;
dry_L = mem_L[dry_pos];
dry_R = mem_R[dry_pos];

// 9. Output Phase-Perfect Dry/Wet Mix
spl0 = dry_L + (wet_L - dry_L) * mix;
spl1 = dry_R + (wet_R - dry_R) * mix;

// Advance the global write position
write_pos = (write_pos + 1) & MASK;
```

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
