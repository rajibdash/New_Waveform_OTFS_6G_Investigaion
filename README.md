# New_Waveform_OTFDM_6G_Investigaion

## Orthogonal Time Frequency Multiplexing (OTFDM)

This repository contains the paper **“Orthogonal Time Frequency Multiplexing (OTFDM): A Novel Waveform Targeted for IMT-2030”** by Koteswara Rao Gudimitla, Sibgath Ali Khan M, and Kiran Kuchi. The paper proposes OTFDM as a candidate 6G/IMT-2030 waveform for low-latency, high-mobility, and power-efficient communications.

> **Terminology:** The paper calls the proposed waveform **OTFDM** (Orthogonal Time Frequency Multiplexing). Although “OTFS” appears in this repository’s name, this document describes OTFDM, not the distinct Orthogonal Time Frequency Space (OTFS) waveform.

## Paper

- [Read the paper (PDF)](OTFDM%2C%20a%20new%20waveform%20for%206G.pdf)
- arXiv: [2409.01114v2](https://arxiv.org/abs/2409.01114v2) (September 13, 2024)
- Authors: Koteswara Rao Gudimitla, Sibgath Ali Khan M, and Kiran Kuchi

## What OTFDM proposes

OTFDM time-multiplexes a reference signal (RS) and user data within **one transmission symbol**. It DFT-precodes the combined sequence, adds excess bandwidth, applies frequency-domain square-root-raised-cosine (SQRC) spectrum shaping, and uses an OFDM IFFT and cyclic prefix to generate the transmitted waveform. A cyclic prefix and cyclic suffix around the RS help isolate it for channel estimation. Optional additional reference signals (ARS) can be included to track residual phase changes at high mobility.

The receiver applies the matched shaping filter and folds the expanded spectrum back to symbol rate, reconstructs the time-domain RS/data sequence, estimates and interpolates the channel, and MMSE-equalizes the folded signal. ARS can be used to estimate and correct residual phase or frequency offset. The paper uses FFT/IFFT-based processing throughout.

### Motivation and claimed benefits

- **Low latency:** data and RS are sent in the same symbol, enabling a self-contained, single-symbol transmission rather than reserving a separate symbol for RS.
- **Mobility:** per-symbol channel estimation and optional ARS phase tracking are intended to improve robustness to Doppler and rapid channel variation.
- **Power efficiency:** spectrum shaping and excess bandwidth localize the effective pulse and reduce channel-estimation leakage; the reported PAPR is comparable to or lower than DFT-s-OFDM.
- **RS overhead:** the RS length and excess bandwidth can be selected by modulation order, instead of using a fixed RS-symbol allocation.

These are results and design claims from the paper’s link-level simulations; they are not deployment guarantees or a claim that OTFDM has been standardized.

## Simulation setup

The paper evaluates a 7 GHz carrier, 100/200/400 MHz bandwidths, and 30/60/120 kHz subcarrier spacing (SCS). It considers one-symbol transmissions, one transmit and one receive antenna, and π/2-BPSK, QPSK, 16-QAM, 64-QAM, and 256-QAM. The channels include 3GPP TDL-C with 1 μs and 260 ns delay spreads and a high-speed-train (HST) model. Reported metrics include block error rate (BLER), channel-estimation mean squared error (MSE), RS overhead, and PAPR.

### Test parameter matrix and reproducibility checklist

Use the following as the test matrix when implementing or reproducing the link-level evaluation. Run the paper's stated combinations where practical, and record the exact value of every setting; the paper summary does not specify every implementation detail. Do not treat unspecified settings below as values reported by the paper.

| Parameter | Paper-stated values or test coverage |
|---|---|
| Carrier frequency | 7 GHz |
| Occupied/system bandwidth | 100, 200, and 400 MHz; record the exact definition used and resulting active subcarriers |
| Subcarrier spacing | 30, 60, and 120 kHz |
| Transmission length | One OTFDM symbol; test longer bursts separately if the implementation supports them |
| Antennas | SISO, 1 transmit and 1 receive antenna |
| Modulation | π/2-BPSK, QPSK, 16-QAM, 64-QAM, and 256-QAM |
| Channel | 3GPP TDL-C with 1 μs and 260 ns delay spreads, and the paper's HST model |
| Mobility | Include the paper's reported mobility points/results for each applicable SCS and modulation; for HST, include up to 500 km/h. Record the speed used for every run |
| RS and excess bandwidth | Use the modulation-specific settings in the table below for the reported TDL-C 1 μs, 30 kHz case; do not assume they apply unchanged to every case |
| Additional reference signal (ARS) | Compare disabled against the paper's approximately 1–2% resource allocation in the HST/high-mobility case |
| Waveform baseline | Compare against DFT-s-OFDM with the same payload, modulation, bandwidth, SCS, channel, power convention, and receiver assumptions |

For each run, also pin down and report these controls because they are required for reproducibility but are not fully enumerated in the README's paper summary:

- **Grid and sample generation:** FFT size, active/guard subcarriers, sample rate, cyclic-prefix and cyclic-suffix lengths, RS/data positions and lengths, resource mapping, and any padding.
- **OTFDM processing:** DFT/IFFT sizes and normalization, excess-bandwidth definition, SQRC filter/roll-off and coefficients, truncation/windowing, and matched-filter/folding implementation. Include extension settings of 0%, 5%, and 10% when reproducing the reported RS/PAPR cases, and state precisely how extension is defined.
- **Power and receiver:** energy normalization (per data symbol, information bit, or total transmit energy), SNR definition, noise variance, channel-estimation and interpolation method, MMSE equalizer settings, synchronization assumptions, and whether CFO, phase noise, or channel-estimation errors are modeled.
- **Coding and traffic:** coding scheme/rate or uncoded operation, transport-block size, payload size, and whether results include coding/CRC overhead. Keep these fixed across waveform comparisons.
- **Sweep and statistics:** SNR range and step, channel realizations, bits/blocks per point, random seeds, stopping rule, and confidence intervals. Use enough errors/trials to make BLER tails and channel-estimation MSE comparisons statistically meaningful.
- **Metric definitions:** BLER denominator and error event, channel-estimation MSE normalization/reference points, RS/ARS overhead denominator, and PAPR measurement point, oversampling factor, and CCDF sample count. Report PAPR at the 1% CCDF point when comparing with the paper.

Keep the paper's reproduced configuration distinct from any expanded stress tests (for example, multiple antennas, longer bursts, additional impairments, or alternate channels). This avoids presenting a new test assumption as a published OTFDM result.

## Selected reported results

### RS overhead and excess bandwidth

For the TDL-C 1 μs channel at 30 kHz SCS, the paper reports the following modulation-specific settings:

| Modulation | RS overhead | Excess bandwidth |
|---|---:|---:|
| π/2-BPSK | 4.9% | 0% |
| QPSK | 5.4% | 0% |
| 16-QAM | 7.0% | 0% |
| 64-QAM | 12.7% | 5% |
| 256-QAM | 13.5% | 5% |

The paper reports BLER performance comparable to DFT-s-OFDM in its low-mobility comparison. It also compares the OTFDM RS overheads with a 28.6% DMRS allocation (4 of 14 symbols) for the DFT-s-OFDM configuration discussed in the paper; this is a comparison of those evaluated configurations, not a universal overhead for every DFT-s-OFDM system.

### High-mobility results

The following are the paper’s recommended speeds for the TDL-C channel with a 260 ns maximum delay spread:

| SCS | π/2-BPSK | QPSK | 16-QAM | 64-QAM | 256-QAM |
|---:|---:|---:|---:|---:|---:|
| 30 kHz | >500 km/h | >500 km/h | >500 km/h | 100 km/h | 30 km/h |
| 60 kHz | >500 km/h | >500 km/h | >500 km/h | 160 km/h | 70 km/h |
| 120 kHz | >500 km/h | >500 km/h | >500 km/h | 300 km/h | 100 km/h |

For the separate HST-model evaluation at 7 GHz and 30 kHz SCS, the paper reports that adding ARS equal to roughly 1–2% of allocated resources can support up to 500 km/h, including higher-order modulation, without the reported BLER degradation. These results depend on the modeled channel, SNR, and simulation assumptions.

**Source discrepancy:** the paper’s prose states that 256-QAM at 120 kHz SCS supports 160 km/h, while its Table III reports 100 km/h. The table above reproduces Table III; the paper does not reconcile the difference.

### PAPR gain

At the 1% CCDF point, the paper reports these PAPR gains relative to DFT-s-OFDM:

| Modulation | 5% extension | 10% extension |
|---|---:|---:|
| QPSK | 0.21 dB | 0.47 dB |
| 16-QAM | 0.16 dB | 0.28 dB |
| 64-QAM | 0.13 dB | 0.23 dB |
| 256-QAM | 0.125 dB | 0.21 dB |

For π/2-BPSK, the paper describes operation near PA saturation without excess bandwidth. For the other modulations, the reported PAPR is similar to or modestly better than DFT-s-OFDM, with the gain depending on the extension factor.

## Scope

The repository currently provides the paper PDF; it does not include a reference implementation, simulation scripts, or reproducibility package. For the detailed signal model, receiver algorithms, figures, and full simulation assumptions, consult the [paper](OTFDM%2C%20a%20new%20waveform%20for%206G.pdf).
