---
layout: post
title: Audio Equalizer Circuit Design
description: Adjustable audio equalizer circuit design project with 2nd order Sallen-Key filter topology.
date: 2026-05-02 12:00 -0400
categories: [Projects, ECE]
tags: [circuit design, team]
math: true
image:
  path: assets/posts/equalizer/final-circuit.png
---

## Introduction

The objective of this project is to design and construct an analog audio equalizer. An audio equalizer is a system used to adjust the gain of specific frequency ranges, and allows the tuning and enhancement of audio quality. The project calls for three adjustable audio bands to control low, middle, and high frequencies, as well as overall volume control; the signal is filtered into individual frequency bands, and the channel and overall volumes are controlled using operational amplifiers. Additionally, the system uses a power amplifier to boost the signal to a higher output level to be used in speakers. The low frequencies are defined as <320 Hz, middle frequencies are 320-3200 Hz, and high frequencies are >3200 Hz. The combined equalizer output must be 100 mV$$_\text{RMS}$$ with all frequencies at maximum, and less than 15 mV$$_\text{RMS}$$ at minimum. Additionally, the power amplifier must be capable of outputting greater than 400 mW of power at maximum volume.

Audio equalizers are used in a variety of applications in signal processing. The ability to fine-tune various frequency ranges with an audio equalizer allows for increased sound quality, enhanced tone, and noise reduction. Music production utilizes equalizers to balance audio frequencies, although typically the processing is done digitally. Analog audio equalizers are still present in some musical instruments (guitar amplifiers and pedals especially) and some older sound systems.

---

## Theory

In this project, active filters were chosen over passive filters, since they offer built-in buffering and steeper cutoffs with only a few more components.

### Sallen-Key Topology

Sallen-Key topology is a second-order active filter design, valued for its simplicity, designed by R.P. Sallen and E. L. Key of MIT Lincoln Laboratory in 1955. All filters shown in the theory section are of the unity-gain configuration, which means that the output has a maximum gain of 0 dB. The Quality Factor $$Q$$ is a parameter that determines the height and width of the peak of the frequency response of the filter. For this application, a $$Q$$ factor of $$1/\sqrt{2} \approx 0.7071$$ is desired — this is named the Butterworth configuration and leads to a maximally flat passband.

![Generic Sallen-Key filter topology]({{ site.baseurl }}/assets/posts/equalizer/2_Theory/sallen-key_generic_circuit.png)
*Figure 1: Generic Sallen-Key filter topology.*

The transfer function of the generic Sallen-Key topology is as follows:

$$\frac{v_{out}}{v_{in}} = \frac{Z_3 Z_4}{Z_1 Z_2 + Z_3(Z_1 + Z_2) + Z_3 Z_4}.$$

---

#### Sallen-Key Low-Pass Filter

![Generic Sallen-Key low-pass filter topology]({{ site.baseurl }}/assets/posts/equalizer/2_Theory/Sallen-Key_Lowpass_General.png)
*Figure 2: Generic Sallen-Key low-pass filter topology.*

Conceptually, this filter works as follows: at high frequencies, the capacitors act as closed circuits, so the signal is shunted straight to ground through $$C_1$$ and $$C_2$$; at low frequencies, the circuit simply passes the signal straight through with no gain, since the capacitors act like open circuits. The op-amp buffers the output, preventing loading from disturbing the filter's frequency response. The specific component impedances are:

The Quality Factor $$Q$$ and cutoff frequency $$f_0$$ can be calculated as

f_0 = \frac{1}{\sqrt{R_1 R_2 C_1 C_2}}$$

and

$$Q = \frac{\omega_0}{2\alpha} = \frac{\sqrt{R_1 R_2 C_1 C_2}}{C_2 (R_1 + R_2)}.$$

To simplify the design, the following substitutions are made:

$$R_1 = R, \quad R_2 = mR, \quad C_1 = C, \quad C_2 = nC.$$

The expressions for $$f_0$$ and $$Q$$ reduce to

$$f_0 = \frac{1}{2\pi \sqrt{mn} \, RC}$$

and

$$Q = \frac{\sqrt{mn}}{m+1}.$$

Setting $$m = 1$$ and $$Q = 1/\sqrt{2}$$ (Butterworth filter) and solving gives $$n = 2$$. Thus the component relationships are

$$R_1 = R_2 = R, \qquad C_1 = C, \quad C_2 = 2C,$$

and the cutoff frequency simplifies to

$$f_0 = \frac{1}{2\pi\sqrt{2}\,RC}. \tag{1}$$

---

#### Sallen-Key High-Pass Filter

![Generic Sallen-Key high-pass filter topology]({{ site.baseurl }}/assets/posts/equalizer/2_Theory/Sallen-Key_Highpass_General.png)
*Figure 3: Generic Sallen-Key high-pass filter topology.*

Figure 3 shows a general unity-gain high-pass configuration. Conceptually, this filter works similarly to the low-pass filter: at low frequencies, the capacitors act as open circuits, so the signal cannot pass through C1 and C2, and at high frequencies, the circuit simply passes the signal straight through with no gain, since the capacitors act like closed circuits. The op-amp buffers the output of the filter, preventing loading from disturbing the filter’s frequency response. Additionally, the dual capacitors lead to a much steeper cutoff.

Similarly simplifications to the low-pass filter are implemented; the substitutions $$R_1 = R$$, $$R_2 = nR$$, $$C_1 = C$$, $$C_2 = mC$$, and again setting $$m = 1$$, $$Q = 1/\sqrt{2}$$, cause the component relationships to become

$$C_1 = C_2 = C, \qquad R_1 = R, \quad R_2 = 2R$$

and the cutoff frequency equation is the same as in Equation 1.

---

#### Sallen-Key Band-Pass Filter

The simplest band-pass design is a low-pass and high-pass filter in series. Since Sallen-Key filters provide built-in buffering, one filter can simply follow the other with no issues. The high-pass and low-pass cutoffs are selected independently, which is very useful when the band-pass lower and upper cutoffs need to meet specific frequencies.

---

### Operational Amplifier Principles

![Op-amp in negative feedback configuration]({{ site.baseurl }}/assets/posts/equalizer/2_Theory/op_amp_negative_feedback.png)
*Figure 4: An op-amp in negative feedback configuration.*

To control the volume of the signal, op-amps in negative feedback configuration are used. In this configuration, the op-amp uses a portion of the output signal, fed back to the inverting input, to oppose changes at the output. The overall gain is determined by the ratio of the two resistors:

$$\frac{V_{out}}{V_{in}} = -\frac{R_f}{R_{in}}. \tag{8}$$

Since $$R_f$$ is a potentiometer, that ratio can be changed continuously between zero and a designed maximum of $$R_{pot,\,\text{max}}/R_{in}$$. As the potentiometer is rotated, the effective feedback resistance changes, turning it into an adjustable voltage divider which changes how much signal is fed back into the op-amp, allowing the gain (and therefore the output volume) to be tuned manually.

---

### Power Amplifier Principles

To boost the input signal to a level usable to power a speaker, a power amplifier is used.

![LM386 power amplifier schematic]({{ site.baseurl }}/assets/posts/equalizer/2_Theory/power_amp_schematic.png)
*Figure 5: Schematic of the LM386 power amplifier circuit used for volume boosting and control.*

The 10 kΩ potentiometer at the non-inverting input acts as a voltage divider, allowing manual volume control by attenuating $$V_{in}$$ before it reaches the amplifier. The LM386 has an internally fixed gain set by default at 20. The output is coupled to the speaker through a 250 μF capacitor, which blocks any DC offset from reaching the load, while a small RC filter at the output suppresses high-frequency oscillations in the input.

---

## Procedure

### Complete Audio Equalizer Schematic

The circuit was first designed and simulated in LTSpice:

![Full equalizer schematic]({{ site.baseurl }}/assets/posts/equalizer/3_Procedure/schematic_full_labeled.png)
*Figure 6: Schematic for the complete audio equalizer circuit, with labels for each part.*

The signal processing can be seen in the schematic: highs are filtered in the top row, mids in the middle, and lows in the lowest row. Each row contains the Sallen-Key filter for its respective frequency range, which is followed by a volume booster for each. The mids require an inverting amplifier after the two filter parts (low and high pass) to prevent them from destructively interfering with the other signals at the summing amplifier due to phase
changes from the filters. Then the signals are combined with a summing amplifier, and then volume is controlled with a potentiometer set up as a voltage divider before feeding into the audio amplifier. Afterwards, there is some final filtering for increased audio quality before the signal reaches the output speaker.

---

### Design Parameters

The project must meet the following specifications with an input voltage of 320 mV$$_\text{RMS}$$:

| Specification | Requirement |
|---|---|
| Speaker impedance | 8 Ω |
| Bass filter −3 dB cutoff | 320 Hz ± 10% |
| Mid filter −3 dB cutoffs | 320 Hz – 3200 Hz ± 10% |
| Treble filter −3 dB cutoff | 3200 Hz ± 10% |
| $$V_{amp}$$ (min volume) | < 15 mV$$_\text{RMS}$$ @ 100, 1000, 10000 Hz |
| $$V_{amp}$$ (max volume) | 100 mV$$_\text{RMS}$$ ± 10% @ 100, 1000, 10000 Hz |
| Maximum ripple | 15 mV$$_\text{RMS}$$ |
| Output power | > 400 mW from 100 Hz to 10000 Hz |

#### Filter Design Parameters

A python script was used to find optimal R and C value combinations with the smallest % error. The following values were calculated for the filters to be as close as possibe to the desired cutoffs:

| Filter Configuration | Resistors (Ω) | Capacitors (F) |
|---|---|---|
| Low-pass stage | $$R_1 = R_2 = 3.3\text{ k}$$ | $$C_1 = 0.1\,\mu$$, $$C_2 = 0.2\,\mu$$ |
| Band-pass (low-pass section) | $$R_1 = R_2 = 3.3\text{ k}$$ | $$C_1 = 0.01\,\mu$$, $$C_2 = 0.02\,\mu$$ |
| Band-pass (high-pass section) | $$R_1 = 3.3\text{ k}$$, $$R_2 = 6.6\text{ k}$$ | $$C_1 = C_2 = 0.1\,\mu$$ |
| High-pass stage | $$R_1 = 3.3\text{ k}$$, $$R_2 = 6.6\text{ k}$$ | $$C_1 = C_2 = 0.01\,\mu$$ |

*Table 1: Component values for filter configurations.*

The expected filter cutoff values are shown below and compared to the desired values. This is the closest configuration possible with the available resistor and capacitor values.

| Filter Configuration | Desired $$f_c$$ (Hz) | Calculated $$f_c$$ (Hz) | % Error |
|---|---|---|---|
| Low-pass stage | 320 | 341.03 | 6.57% |
| Band-pass (low-pass section) | 3200 | 3410.3 | 6.57% |
| Band-pass (high-pass section) | 320 | 341.03 | 6.57% |
| High-pass stage | 3200 | 3410.3 | 6.57% |

*Table 2: Calculated cutoff frequencies and percentage error.*

These values were deemed acceptable since the percent error is within the ±10% allowed by the design specifications.

#### Volume Control Design Parameters

The amplifier chain is required to produce a minimum output below 15 mVRMS and a maximum of 100 mVRMS ±10% at 100 Hz, 1 kHz, and 10 kHz. These are met through the two stages of gain control described above: the negative feedback op-amp stage with the potentiometer as Rf , followed by the LM386 input attenuator. At minimum volume, the feedback potentiometer is wound to near zero, driving Rf ≈ 0 and therefore Vout/Vin ≈ 0 from Equation 8. Since the potentiometer spans 0–10 kΩ, the minimum output is easily brought below the 15 mVRMS threshold without any special tuning. At maximum volume, the gain must be set such that a 320 mVRMS input produces 100 mVRMS at the output. The required gain is therefore ≈ 0.31, and this gives a theoretical resistance value of approximately 32.3 kΩ. The nearest standard resistor value of 33 kΩ was selected to make sure that the output remained within the ±10% tolerance of the 100 mVRMS target across all test frequencies.

#### Signal Recombination Design Parameters

The signal recombination stage is implemented as a summing amplifier. With multiple inputs each connected through their own input resistor to the inverting terminal, the output is the weighted sum of all input signals. All channels are weighted equally, and the filters are tuned so the cutoffs are very close together, resulting in very low ripple across the frequency range.

---

### Equalizer Operation

To use the circuit, an input signal is provided via the 3.5 mm audio jack or a signal generator. The potentiometers at each channel independently adjust each frequency range, and the final potentiometer feeding into the power amplifier controls overall volume. When connected to a speaker, the circuit plays music with good, adjustable quality.

---

## Results

### Low-Pass Filter FRA

![Low-pass filter frequency response]({{ site.baseurl }}/assets/posts/equalizer/4_Results/FRA_cutoff_low.png){: width="600" }
*Figure 7: Frequency response analysis of the low-pass filter, showing the measured cutoff frequency.*

The low-pass filter had a target cutoff of 320 Hz and a calculated value of 341 Hz. The measured cutoff from the FRA was 375 Hz — a deviation of approximately 10% from the calculated value, and about 17% from the required value, attributable to component tolerances. The response shows the characteristic behavior of a 2nd-order Butterworth Sallen-Key topology: a flat passband with low ripple up to the cutoff, followed by a very steep rolloff.

### Mid-Pass Filter FRA

![Mid-pass filter frequency response]({{ site.baseurl }}/assets/posts/equalizer/4_Results/FRA_cutoff_mid.png){: width="600" }
*Figure 8: Frequency response analysis of the mid-pass filter, showing the measured cutoff frequencies.*

The mid-pass filter had target cutoffs of 320 Hz and 3200 Hz, with calculated values of 341 Hz and 3410 Hz. The measured cutoffs were approximately 375 Hz and 3500 Hz, deviating by about 10% and 2% from the calculated values, and about 17% and 9% from the required values. The lower cutoff is outside the ±10% specification, but the upper cutoff is within the requirement.

### High-Pass Filter FRA

![High-pass filter frequency response]({{ site.baseurl }}/assets/posts/equalizer/4_Results/FRA_cutoff_high.png){: width="600" }
*Figure 9: Frequency response analysis of the high-pass filter, showing the measured cutoff frequency.*

The high-pass filter had a target cutoff of 3200 Hz. The measured cutoff was 3500 Hz — a deviation of approximately 2.6% from the calculated value and about 9% from the required value, within the ±10% specification.

---

### Ripple Measurement

![Ripple FRA]({{ site.baseurl }}/assets/posts/equalizer/4_Results/Vamp_ripple_FRA.png){: width="600" }
*Figure 16: Frequency response analysis of the ripple across the 100 Hz to 10 kHz range.*

![Ripple at 100 Hz (lowest gain)]({{ site.baseurl }}/assets/posts/equalizer/4_Results/Vamp_max_100Hz.png){: width="600" }
*Figure 17: Ripple measurement at 100 Hz — the frequency with the lowest gain response.*

![Ripple at 430 Hz (highest gain)]({{ site.baseurl }}/assets/posts/equalizer/4_Results/Vamp_ripple_430Hz.png){: width="600" }
*Figure 18: Ripple measurement at 430 Hz — the frequency with the highest gain response.*

The FRA shows a sweeping response with peaks near the crossover frequencies between the low/high bands and the mid band. One anomaly is that the FRA shows 100 Hz with a slightly lower gain than 10 kHz, which is inconsistent with the direct oscilloscope measurements that showed the 10 kHz output 9 mV$$_\text{RMS}$$ lower; this discrepancy is likely from breadboard or power supply variation.

### Ripple Calculation

Output was measured at the two gain extremes identified by the FRA: 430 Hz (highest) and 100 Hz (lowest), yielding 108.7 mV$$_\text{RMS}$$ and 101 mV$$_\text{RMS}$$ respectively:

$$V_{amp,max} - V_{amp,min} = 108.7 - 101.0 = 7.7\text{ mV}_\text{RMS}. \tag{11}$$

This is well within the 15 mV$$_\text{RMS}$$ limit.

---

### Results Summary and Error Analysis

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

The dominant source of error is component tolerance. The resistors and capacitors carry a ±5% rating, which in a second-order Sallen-Key topology compounds across two reactive elements, producing cutoff frequency deviations of up to ±10% in the nominal case. All filter cutoffs measured above their targets, suggesting the specific components used fell toward the same edge of their tolerance bands. The low-pass and mid-pass lower cutoff deviations of 17.2% slightly exceed the ±10% specification, while the high-pass and mid-pass upper cutoffs fall within it at 9.4%.

The $$V_{amp}$$ maximum results are excellent, with errors of 1% and 2% at 100 Hz and 1 kHz. The ripple of 7.7 mV$$_\text{RMS}$$ is well within the 15 mV$$_\text{RMS}$$ limit. Both power amplifier measurements exceed the 400 mW requirement.

---

## Conclusion

The experimental results for the audio equalizer circuit were very pleasing. While the filter cutoffs had some error from the calculated values due to component tolerances, all other specifications were met very well. Best of all, the sound quality through the speaker was excellent — multiple TAs commented that the clarity of the sound was some of the best they had heard that semester. This could be attributed to the superior response of the Sallen-Key filters, the signal processing chain before the speaker, or the very small output ripple.

Planning and simulated everything ahead of time in LTSpice greatly accelerated the project. It was very fun researching the Sallen-Key filters, and very rewarding when we had great sound output. I am looking forward to future projects and classes where I will explore these topics in further detail.