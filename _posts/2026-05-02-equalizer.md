---
layout: post
title: Audio Equalizer Circuit Design
description: Adjustable audio equalizer circuit design project.
abstract: Designed and built an analog 3-band audio equalizer using Sallen-Key active filters to meet strict design requirements. Designed and simulated circuit in LTSpice, and constructed and tested physical circuit.
date: 2026-05-02 12:00 -0400
categories: [Projects, ECE]
tags: [circuit design, LTSpice, analog design, hardware testing]
math: true
image:
  path: assets/posts/equalizer/final-circuit.png
---

{% include abstract.html %}

## Introduction

The objective of this project was to design and construct an analog audio equalizer, which is a system used to adjust the gain of specific frequency ranges of an input signal, and is typically used for audio processing. The project called for three adjustable audio bands to control low, middle, and high frequencies, as well as overall volume control. Additionally, the system used a power amplifier to boost the signal to a higher output level to be used in speakers. 

While the minimum project requirement was to use passive first-order filters, active second-order Sallen-Key filtering was chosen instead for the following reasons:

- Sallen-Key filters provide steeper cutoffs since they are second-order filters
- Sallen-Key filters provide built-in buffering, making the extra component count much less significant as the passive filters require op-amps for buffering
- They provide the opportunity to explore higher-order active filtering techniques outside the scope of the project's requirements

---

## Procedure

### Design Parameters

The project must meet the following specifications with an input voltage of 320 mV$$_\text{RMS}$$:

| Specification | Requirement |
|---|---|
| Bass filter −3 dB cutoff | 320 Hz ± 10% |
| Mid filter −3 dB cutoffs | 320 Hz – 3200 Hz ± 10% |
| Treble filter −3 dB cutoff | 3200 Hz ± 10% |
| $$V_{amp}$$ (min volume) | < 15 mV$$_\text{RMS}$$ @ 100, 1k, 10k Hz |
| $$V_{amp}$$ (max volume) | 100 mV$$_\text{RMS}$$ ± 10% @ 100, 1k, 10k Hz |
| Maximum ripple | 15 mV$$_\text{RMS}$$ |
| Output power | > 400 mW from 100 Hz to 10000 Hz |

### Complete Audio Equalizer Schematic

The circuit was first designed and simulated in LTSpice:

![Full equalizer schematic]({{ site.baseurl }}/assets/posts/equalizer/3_Procedure/schematic_full_labeled.png)
*Figure 6: Schematic for the complete audio equalizer circuit, with labels for each part.*

The signal processing can be seen in the schematic: highs are filtered in the top row, mids in the middle, and lows in the lowest row. Each row contains the Sallen-Key filter for its respective frequency range, which is followed by a volume booster for each. The mids require an inverting amplifier after the two filter parts (low and high pass) to prevent them from destructively interfering with the other signals at the summing amplifier due to phase changes from the filters. Then the signals are combined with a summing amplifier, and then volume is controlled with a potentiometer set up as a voltage divider before feeding into the audio amplifier. Afterwards, there is some final filtering for increased audio quality before the signal reaches the output speaker.

---

#### Filter Design Parameters

A Python script was used to find optimal R and C value combinations with the smallest % error. The following values were calculated for the filters to be as close as possibe to the desired cutoffs. 
The expected filter cutoff values are shown below and compared to the desired values. This is the closest configuration possible with the available resistor and capacitor values.

| Filter Configuration | Desired $$f_c$$ (Hz) | Calculated $$f_c$$ (Hz) | % Error |
|---|---|---|---|
| Low-pass stage | 320 | 341.03 | 6.57% |
| Band-pass (low-pass section) | 3200 | 3410.3 | 6.57% |
| Band-pass (high-pass section) | 320 | 341.03 | 6.57% |
| High-pass stage | 3200 | 3410.3 | 6.57% |

*Table 1: Calculated cutoff frequencies and percentage error.*

These values were deemed acceptable since the percent error is within the ±10% allowed by the design specifications.

---

### Equalizer Operation

To use the circuit, an input signal was provided via the 3.5 mm audio jack or a signal generator. The potentiometers at each channel independently adjust each frequency range, and the final potentiometer feeding into the power amplifier controls overall volume. When connected to a speaker, the circuit should plays music with good, adjustable quality.

---

## Results

### Low-Pass Filter FRA

![Low-pass filter frequency response]({{ site.baseurl }}/assets/posts/equalizer/4_Results/FRA_cutoff_low.png){: width="600" }
*Figure 7: Frequency response analysis of the low-pass filter showing measured cutoff frequency.*

The low-pass filter had a target cutoff of 320 Hz and a calculated value of 341 Hz. The measured cutoff from the FRA was 375 Hz, which is a deviation of approximately 10% from the calculated value, and about 17% from the required value. The response shows the characteristic behavior of a 2nd-order Butterworth Sallen-Key topology, with a flat passband with low ripple up to the cutoff, followed by a very steep rolloff.

### Mid-Pass Filter FRA

![Mid-pass filter frequency response]({{ site.baseurl }}/assets/posts/equalizer/4_Results/FRA_cutoff_mid.png){: width="600" }
*Figure 8: Frequency response analysis of the mid-pass filter showing measured cutoff frequencies.*

The mid-pass filter had target cutoffs of 320 Hz and 3200 Hz, with calculated values of 341 Hz and 3410 Hz. The measured cutoffs were approximately 375 Hz and 3500 Hz, deviating by about 10% and 2% from the calculated values, and about 17% and 9% from the required values.

### High-Pass Filter FRA

![High-pass filter frequency response]({{ site.baseurl }}/assets/posts/equalizer/4_Results/FRA_cutoff_high.png){: width="600" }
*Figure 9: Frequency response analysis of the high-pass filter showing measured cutoff frequency.*

The high-pass filter had a target cutoff of 3200 Hz. The measured cutoff was 3500 Hz, which is a deviation of approximately 2.6% from the calculated value and about 9% from the required value.

---

### Ripple Measurement

![Ripple FRA]({{ site.baseurl }}/assets/posts/equalizer/4_Results/Vamp_ripple_FRA.png){: width="600" }
*Figure 16: Frequency response analysis of the ripple across the 100 Hz to 10 kHz range.*

![Ripple at 100 Hz (lowest gain)]({{ site.baseurl }}/assets/posts/equalizer/4_Results/Vamp_max_100Hz.png){: width="600" }
*Figure 17: Ripple measurement at 100Hz, the lowest gain response frequency.*

![Ripple at 430 Hz (highest gain)]({{ site.baseurl }}/assets/posts/equalizer/4_Results/Vamp_ripple_430Hz.png){: width="600" }
*Figure 18: Ripple measurement at 430 Hz, the highest gain response frequency.*

The FRA shows a sweeping response with peaks near the crossover frequencies between the low/high bands and the mid band. One anomaly is that the FRA shows 100 Hz with a slightly lower gain than 10 kHz, which is inconsistent with the direct oscilloscope measurements that showed the 10 kHz output 9 mV$$_\text{RMS}$$ lower.

### Ripple Calculation

Output was measured at the two gain extremes identified by the FRA: 430 Hz (highest) and 100 Hz (lowest), yielding 108.7 mV$$_\text{RMS}$$ and 101 mV$$_\text{RMS}$$ respectively:

$$V_{amp,max} - V_{amp,min} = 108.7 - 101.0 = 7.7\text{ mV}_\text{RMS}. \tag{11}$$

This is well within the 15 mV$$_\text{RMS}$$ limit.

---

### Results Summary

| Parameter | Requirement | Measured | Error |
|---|---|---|---|
| Low-pass cutoff | 320 Hz ± 10% | 375 Hz | +17.2% |
| Mid-pass lower cutoff | 320 Hz ± 10% | 375 Hz | +17.2% |
| Mid-pass upper cutoff | 3200 Hz ± 10% | 3500 Hz | +9.4% |
| High-pass cutoff | 3200 Hz ± 10% | 3500 Hz | +9.4% |
| $$V_{amp}$$ min (100 Hz) | < 15 mV$$_\text{RMS}$$ | ≈ 0 mV$$_\text{RMS}$$ | — |
| $$V_{amp}$$ min (1 kHz) | < 15 mV$$_\text{RMS}$$ | ≈ 0 mV$$_\text{RMS}$$ | — |
| $$V_{amp}$$ min (10 kHz) | < 15 mV$$_\text{RMS}$$ | ≈ 0 mV$$_\text{RMS}$$ | — |
| $$V_{amp}$$ max (100 Hz) | 100 mV$$_\text{RMS}$$ ± 10% | 101 mV$$_\text{RMS}$$ | +1.0% |
| $$V_{amp}$$ max (1 kHz) | 100 mV$$_\text{RMS}$$ ± 10% | 102 mV$$_\text{RMS}$$ | +2.0% |
| $$V_{amp}$$ max (10 kHz) | 100 mV$$_\text{RMS}$$ ± 10% | 92 mV$$_\text{RMS}$$ | −8.0% |
| Ripple | ≤ 15 mV$$_\text{RMS}$$ | 7.7 mV$$_\text{RMS}$$ | — |
| Output power (1 kHz) | > 400 mW | 451 mW | — |
| Output power (10 kHz) | > 400 mW | 605 mW | — |

*Table 3: Summary of measured results against specifications.*

The three main sources of error are component tolerances, non-ideal op-amps, and breadboard imperfections. The resistors and capacitors carry a ±5% value rating, which in a second-order Sallen-Key topology could compounds across two reactive elements and producing cutoff frequency deviations. Also, the cutoff values were calculated assuming ideal op-amps with no internal resistance and infinite slew rate and bandwidth; the fininte constraints of real op-amps will lead to some deviation from the calculated values due to these imperfections. Finally, the breadboard caused frequent connection issues, and 

The $$V_{amp}$$ maximum results are excellent, with errors of 1% and 2% at 100 Hz and 1 kHz. The ripple of 7.7 mV$$_\text{RMS}$$ is well within the 15 mV$$_\text{RMS}$$ limit. Both power amplifier measurements exceed the 400 mW requirement.

---

## Conclusion and Takeaways

The experimental results for the audio equalizer circuit were very pleasing. While the filter cutoffs had some error from the calculated values due to component tolerances, all other specifications were met very well. Best of all, the sound quality through the speaker was excellent — multiple Teaching Assistants commented that the clarity of the sound was some of the best they had heard that semester. This could be attributed to the superior response of the Sallen-Key filters, the signal processing chain before the speaker, or the very small output ripple.

This project also taught me a lot about analog circuit design, simulation, and testing techniques. The skills I've learned from this project will be invaluable as I continue through my Engineering journey.