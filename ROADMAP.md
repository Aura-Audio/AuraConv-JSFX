# 🗺️ AuraConv-JSFX Strategic Development Roadmap - v1.0.0

**Vision Statement:** To evolve AuraConv from a highly optimized moving-average utility into a premier, platform-independent sound design and transient-shaping tool, while maintaining zero-artifact automation and pristine DSP standards.

---

## 🟢 Phase 1: Acoustic Refinement & Musicality (v3.5)
*Target: Q4 2026*

The immediate focus is to address the mathematical quirks inherent to moving average (boxcar) filters and improve cross-compatibility across different audio environments.

*   **Sample Rate Independence (`ms` instead of `samples`)**
    *   *The Feature:* Transition the `Filter Length` slider from absolute samples (e.g., 1000 taps) to milliseconds (e.g., 20.0 ms).
    *   *DSP Value:* Currently, a 1000-tap filter sounds completely different at 44.1kHz than it does at 96kHz. By calculating the buffer size dynamically based on the host's `srate` variable, the sonic character of the blur will remain 100% consistent regardless of the project's sample rate.
*   **Windowing Functions (Sidelobe Suppression)**
    *   *The Feature:* Introduce a drop-down menu to select the convolution window shape (Boxcar, Hann, Hamming, Blackman).
    *   *DSP Value:* A standard moving average (Boxcar) creates a `sinc` frequency response, resulting in metallic, resonant sidelobes. Implementing weighted windowing will smooth the roll-off, resulting in a much warmer, analog-style "muffle" without the hollow comb-filtered artifacts.
*   **Phase-Inverted High-Pass Mode**
    *   *The Feature:* A toggle to output the mathematical difference between the Dry signal and the Wet signal.
    *   *DSP Value:* Because v3 mathematically phase-aligns the wet and dry signals, subtracting the moving average from the dry signal yields a perfectly linear-phase High-Pass filter. This essentially turns AuraConv into a transient-extractor.

---

## 🟡 Phase 2: Visuals & Dynamic Ecosystem (v4.0)
*Target: Q2 2027*

Transitioning from REAPER’s default slider interface to a fully custom graphical UI, while introducing dynamic modulation capabilities to make the plugin "breathe" with the music.

*   **Custom Graphical User Interface (GUI)**
    *   *The Feature:* Utilizing JSFX’s `@gfx` library to build a modern, hardware-accelerated interface.
    *   *UX Value:* Will feature a real-time waveform visualizer showing the transient smearing, smooth rotary knobs instead of basic sliders, and a sleek, dark-mode vector aesthetic to match premium commercial plugins.
*   **Integrated Envelope Follower (Dynamic Blur)**
    *   *The Feature:* An onboard envelope follower that tracks the input volume and dynamically modulates the `Filter Length` or `Dry/Wet Mix`.
    *   *DSP Value:* Allows for "Ducking Blur"—where the transient hits cleanly (dry), but the sustain gets heavily blurred (wet), effectively creating a custom reverb/diffusion tail derived purely from the source audio.
*   **Mid/Side Processing Matrix**
    *   *The Feature:* Toggle to process Stereo (L/R) or Mid/Side (M/S).
    *   *DSP Value:* Allows users to heavily blur the stereo width (Side) to push it to the background, while keeping the center channel (Mid) punchy and completely unprocessed.

---

## 🟠 Phase 3: Advanced Architecture & Expansion (v5.0)
*Target: Q4 2027 – Q1 2028*

The final phase involves migrating the core algorithm out of the JSFX ecosystem into compiled C++ for universal DAW compatibility and advanced spectral control.

*   **JUCE Framework Port (VST3, AU, AAX)**
    *   *The Feature:* Rewriting the EEL2 code into highly optimized C++ using the JUCE framework.
    *   *Business/Market Value:* Breaks the plugin out of the REAPER ecosystem, allowing it to be distributed universally to Ableton, Logic Pro, FL Studio, and Pro Tools users.
*   **Multi-Band Processing**
    *   *The Feature:* A Linkwitz-Riley crossover network splitting the audio into High, Mid, and Low bands, each with independent AuraConv processors.
    *   *DSP Value:* Low-frequency blurring often results in muddy, unusable rumble. Multi-band processing will allow users to aggressively blur only the high-frequency "air" or mid-range synths while keeping the sub-bass completely tight and mono-compatible.
*   **FFT Convolution Engine Replacement**
    *   *The Feature:* Moving from a time-domain running sum to a frequency-domain Fast Fourier Transform (FFT) convolution engine.
    *   *DSP Value:* While the current running sum is highly efficient for Boxcar filters, FFT convolution is required to process complex window shapes (Hann, Blackman) at massive sizes (e.g., 20,000+ taps) without overloading the CPU. This will allow for filter lengths of several full seconds, turning AuraConv into a granular freeze/drone machine.

---

## 🛠️ Ongoing Technical Debt & Maintenance
*(Continuous throughout all phases)*

*   **PDC Smoothing:** Investigating host-specific methods for graceful Plugin Delay Compensation (PDC) reporting during active size automation, to prevent DAW transport stutters.
*   **Oversampling Framework:** Building a lightweight 2x/4x oversampling wrapper to prepare for any non-linear features (like saturation) that might be added to the feedback path in future iterations. 
*   **CPU Profiling:** Continuous benchmarking against modern CPU architectures (Apple Silicon ARM, Intel Core/AMD Ryzen) to ensure cache-line optimizations are functioning optimally in the delay-line buffers.
