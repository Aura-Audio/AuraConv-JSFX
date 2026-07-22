# GTM - v1.0.0

---

## 1. Executive Summary

**AuraConv v3** represents a highly optimized, phase-aligned transient blurring and sound design tool. Originally conceived as a simple moving average utility, the v3 architecture elevates the algorithm to professional DSP standards. 

This report assesses the commercial viability of AuraConv v3. While the current JSFX implementation restricts the immediate Serviceable Obtainable Market (SOM) to users of Cockos REAPER, the underlying DSP architecture possesses strong commercial potential. Transitioning the core intellectual property (IP) to a universal format (VST3/AU/AAX) offers a viable path toward establishing a profitable boutique plug-in brand.

---

## 2. Product Positioning & Unique Selling Proposition (USP)

In digital audio, "blurring" is traditionally achieved through spectral processing (FFT), granular synthesis, or long, diffuse convolution reverb tails. AuraConv v3 approaches time-domain smoothing differently, utilizing a continuous, zero-latency linear-phase FIR moving average.

### Core USPs:
* **True Transient Smearing:** Unlike standard low-pass filters that simply dull high-frequency energy, AuraConv smears transients across a user-defined time window, creating a unique "soft-focus" acoustic effect without muddying the signal.
* **Phase-Aligned Parallel Blending:** Traditional time-domain filters create frequency-dependent group delays. AuraConv’s mathematical enforcement of odd-numbered tap lengths paired with a center-aligned delay tap ensures that the Dry/Wet mix is entirely free of comb filtering.
* **Extreme CPU Efficiency:** The implementation of bitwise masking and optimized loop accumulation allows the algorithm to run at a fraction of the CPU cost associated with spectral or multi-tap delay equivalents.

---

## 3. Market Analysis & Target Segmentation

The target market is divided into three distinct segments:

```
                  ┌─────────────────────────────────────────┐
                  │          Total Addressable Market       │
                  │        (Universal VST3/AU/AAX Port)     │
                  └────────────────────┬────────────────────┘
                                       │
         ┌─────────────────────────────┼─────────────────────────────┐
         ▼                             ▼                             ▼
┌─────────────────┐           ┌─────────────────┐           ┌─────────────────┐
│   Game Audio &  │           │    Electronic   │           │ Mixing/Mastering│
│  Sound Designers│           │ Music Producers │           │    Engineers    │
│  (High Affinity)│           │ (Moderate/High) │           │  (Niche Utility)│
└─────────────────┘           └─────────────────┘           └─────────────────┘
```

### Segment A: Game Audio & Sound Designers (High Affinity)
* **Needs:** Fast, highly automated tools to create ambient textures, atmospheric backgrounds, pads, and synthesized foley from dry field recordings.
* **Alignment with AuraConv:** High. This segment values CPU efficiency (essential when running heavy plugin chains in game engines or DAW templates) and relies heavily on real-time parameter automation.
* **REAPER Market Share:** REAPER is the industry-standard DAW for AAA and indie game audio studios, making this segment highly accessible under the current JSFX format.

### Segment B: Electronic & Ambient Music Producers (Moderate to High)
* **Needs:** Unique textural tools, transient-shapers, and spatial effects to create organic "wash" sounds, lofi elements, and pads.
* **Alignment with AuraConv:** High. Producers are constantly seeking boutique, specialized processors that offer distinct sonic characters differing from stock DAW tools.

### Segment C: Mixing & Mastering Engineers (Niche Utility)
* **Needs:** High-precision correction tools.
* **Alignment with AuraConv:** Low-to-Moderate. AuraConv can function as a linear-phase high-pass or low-pass filter, but this is a secondary utility rather than a core creative application.

---

## 4. Market Sizing (TAM, SAM, SOM)

To understand the revenue potential of AuraConv, we look at the audio software plugin market using standard sizing frameworks. Industry data shows a booming independent audio creator segment, with the United States alone accounting for over 3.2 million active music creators utilizing plugin-based workflows.

```
┌────────────────────────────────────────────────────────┐
│  TAM (Total Addressable Market)                        │
│  Global Audio Plugin Software Market                   │
│  ~$1.62 Billion (Growing to ~$3.32 Billion by 2030)    │
├────────────────────────────────────────────────────────┤
│  SAM (Serviceable Addressable Market)                  │
│  Creative Music Production Segment                     │
│  ~$1.16 Billion (41.3% of TAM)                         │
├────────────────────────────────────────────────────────┤
│  SOM (Serviceable Obtainable Market)                   │
│  Boutique Creative DSP Niche (Year 1-2 Port)           │
│  ~$25k - $75k Annual Revenue                           │
└────────────────────────────────────────────────────────┘
```

### Total Addressable Market (TAM)
* **Definition:** The global market for audio plugin software.
* **Size:** The global audio plugin software market reached **$1.62 billion** and is projected to grow to **$3.32 billion by 2030**, exhibiting a strong Compound Annual Growth Rate (CAGR) of **9.6%**. 

### Serviceable Addressable Market (SAM)
* **Definition:** The specific portion of the audio plugin market dedicated strictly to music production, sound design, composition, and mixing workflows.
* **Size:** Music production represents the single largest application domain in this market, capturing **41.3% of the total market share** (equivalent to roughly **$1.16 billion**). This represents the total group of creators who actively buy creative filtering, delay, and spatial processing tools.

### Serviceable Obtainable Market (SOM)
* **Definition:** The realistic fraction of the SAM that AuraConv can capture within its first two years of a commercial C++ (VST3/AU) release.
* **Size:** **$25,000 – $75,000** annually.
* **Contextualizing the SOM:** According to the *State of the Independent Audio Plugin Companies Report*, the boutique plugin landscape is highly stratified:
  * **49%** of developers operate as **Side-Projects** (earning under $20,000/year).
  * **21%** operate as **Independent** (earning $20,000 – $100,000/year).
  * **30%** are **Established** (earning $100,000+ annually).
  
  Transitioning AuraConv v3 to a commercial VST3 format positions the product to capture a healthy share of the "Independent" tier, utilizing highly targeted marketing toward game audio professionals and electronic music producers.

---

## 5. Competitive Landscape

AuraConv sits at the intersection of filtering, transient shaping, and micro-delays. 

| Competitor | Category | Strengths | AuraConv v3 Comparison |
| :--- | :--- | :--- | :--- |
| **Oeksound Spiff** | Spectral Transient Processor | High precision, industry standard. | Spiff is highly complex and expensive ($199). AuraConv offers a simpler, more affordable, time-domain smoothing alternative. |
| **Unfiltered Audio Silo / SpecOps** | Granular / Spectral Reverb | Rich, complex spaces. | High CPU usage and complex interfaces. AuraConv is highly lightweight, direct, and zero-latency. |
| **Valhalla Delay (Diffuser Mode)** | Digital Delay / Diffusion | Warm, lush, high-quality. | Valhalla is highly revered. AuraConv competes on CPU performance and its unique mathematical "boxcar" character. |

---

## 6. SWOT Analysis

### Strengths
* Highly optimized C-style EEL2 code; extremely low CPU footprint.
* Phase-aligned parallel pathing solves the comb-filtering issue common in custom-built scripts.
* Zero-latency operation makes it suitable for live tracking or large templates.

### Weaknesses
* **Platform Lock:** The JSFX format limits the customer base exclusively to REAPER users.
* **IP Exposure:** JSFX code is distributed as open-source plain text, making licensing, encryption, and intellectual property protection difficult without complex obfuscation.

### Opportunities
* **Boutique C++ Conversion:** Porting the DSP code to the JUCE framework opens up access to the entire global DAW market (Pro Tools, Ableton, Logic, FL Studio).
* **Niche Domination in Game Audio:** Marketing specifically to the game audio community via platforms like the Airwiggles community or the Sound Design subreddits.

### Threats
* **Market Saturation:** The boutique audio plugin market is highly competitive and crowded.
* **Freeware Alternatives:** The prevalence of free JSFX scripts makes charging a premium price for a JSFX-only plugin difficult.

---

## 7. Commercialization & Go-To-Market (GTM) Strategy

To maximize commercial returns while respecting technical limitations, a two-phased GTM strategy is proposed.

```
PHASE 1 (Immediate)                     PHASE 2 (Mid-Term)
Establish Brand on JSFX                 Port to Universal VST3/AU
┌───────────────────────────────┐       ┌───────────────────────────────┐
│ • Platform: Gumroad / Patreon │       │ • Platform: JUCE Framework    │
│ • Pricing: $15 - $25          │──────>│ • Pricing: $49 - $69          │
│ • Market: REAPER Game Audio   │       │ • Market: Broad Producer TAM  │
└───────────────────────────────┘       └───────────────────────────────┘
```

### Phase 1: Boutique JSFX Release (Low Risk / Brand Building)
* **Format:** Securely packaged JSFX via a custom ReaPack repository.
* **Pricing Model:** "Pay-What-You-Want" with a suggested price of **$15–$25**, or integrated into a subscription model (e.g., Patreon at $5/month for access to a suite of tools).
* **Objective:** Validate the market, build an active email list of sound designers, and gather user feedback to fund Phase 2.

### Phase 2: C++ Transition & Universal VST3/AU Port (High Return)
* **Format:** Compiled binaries using the JUCE framework.
* **Pricing Model:** Commercial license at **$49** (introductory) / **$69** (retail).
* **Objective:** Enter the mainstream market. Capitalize on the professional-grade DSP (using the roadmap's planned multi-band and custom vector GUI additions) to compete directly with boutique DSP houses like Valhalla DSP or Goodhertz.

---

## 8. Financial Projections & Resource Estimates

### Estimated Costs to Reach Phase 2 (C++ Release)
* **DSP C++ Porting / JUCE Development:** $3,500 – $5,000 (if outsourced; free if self-developed).
* **Graphic Design / UI Design:** $800 – $1,500.
* **Marketing & Launch Budget:** $500 (focused on target YouTube influencers and community forums).
* **Total Estimated Runway Required:** **$4,800 – $7,000**.

### Projected Sales Revenue (Year 1 Post-VST Port)
Assuming a modest conversion rate of a targeted email list and modern boutique plugin marketing:

* **Conservative Scenario (350 units @ $49):** $17,150 gross revenue.
* **Target Scenario (800 units @ $49):** $39,200 gross revenue.
* **Optimistic Scenario (1,500 units @ $49):** $73,500 gross revenue.

---

## 9. Conclusion & Strategic Recommendation

AuraConv v3 possesses sound mathematical foundations and a distinct sonic niche. However, trying to monetize it purely as a JSFX plugin faces significant limitations due to the platform's open-source nature and REAPER-only restriction.

### Recommended Next Steps:
1. **Launch Phase 1 immediately:** Release AuraConv v3 as a premium JSFX on Gumroad/Patreon. Focus marketing heavily on the REAPER game audio community.
2. **Collect Feedback & Build Audience:** Use this initial release to build a verified mailing list of users.
3. **Initiate Phase 2 Planning:** Use the revenue generated from Phase 1 to fund the JUCE C++ port, incorporating the custom vector GUI and millisecond-based sample rate calculations outlined in the roadmap.
