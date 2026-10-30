# OTFS for 6G: an operator-focused investigation

## Executive assessment

Orthogonal time frequency space (OTFS) modulation is worth evaluating for
high-mobility, time-varying radio channels. It is **not a demonstrated universal
replacement** for cyclic-prefix OFDM (CP-OFDM) or DFT-spread OFDM (DFT-s-OFDM).
Its delay–Doppler representation can help a suitably designed receiver exploit
channel diversity, but gains depend on channel estimation, detection, overhead,
and implementation cost.

**Recommendation:** retain CP-OFDM and DFT-s-OFDM as reference baselines; investigate
OTFS first as a targeted option for mobility-limited services. Adopt it only if
end-to-end field measurements demonstrate an operator benefit after accounting
for terminal power, radio/baseband cost, latency, and migration.

This repository contains a desk study, not a simulator, measured benchmark, or
stakeholder survey. No numerical performance gains or commercial savings are
claimed. External sources could not be retrieved during preparation; the dated
references below are supplied for follow-up verification, not presented as
live-verified evidence. In particular, this study does not establish 3GPP's
waveform decisions as of 30 October 2026.

## 1. What changes with OTFS?

- **CP-OFDM:** maps data to time–frequency resources. With a suitable cyclic
  prefix and sufficiently slow channel variation within a symbol, it enables
  simple frequency-domain equalization. Doppler can break subcarrier
  orthogonality and cause intercarrier interference (ICI).
- **DFT-s-OFDM:** adds DFT spreading before OFDM modulation. Its generally lower
  peak-to-average power ratio (PAPR), relative to comparable CP-OFDM signals,
  can reduce power-amplifier back-off. NR uses transform precoding as an uplink
  option [2]. It is not intrinsically immune to Doppler.
- **OTFS:** maps data in delay–Doppler and spreads it across a time–frequency
  block [1]. Joint processing can exploit the channel structure rather than
  treating time variation only as interference. Practical performance still
  depends on pulses, the frame, channel knowledge, and the detector.

These are not always mutually exclusive hardware architectures: OTFS can use
an OFDM-based modulator with additional transforms and processing. The particular
OTFS variant, including its cyclic-prefix and guard structure, must be specified
before comparing efficiency or compatibility.

## 2. Advantages and trade-offs against the legacy baselines

| Criterion | CP-OFDM | DFT-s-OFDM | OTFS: opportunity and qualification |
| --- | --- | --- | --- |
| High mobility / Doppler | ICI grows as channel variation becomes significant relative to symbol duration; numerology and receiver processing can mitigate it. | Also affected by time variation; DFT spreading alone does not solve ICI. | Delay–Doppler-aware detection can improve robustness; fractional Doppler, estimation errors, and detector limits can reduce the gain. |
| Multipath and diversity | CP handles delay spread within its design limit; coding and scheduling exploit diversity. | Frequency-domain equalization and coding remain important. | Spreading and joint detection can exploit delay/Doppler diversity; this is not extra spectrum or free capacity. |
| Terminal transmit efficiency | Typically high PAPR requires amplifier back-off. | Generally favorable PAPR, useful for power-limited uplink. | Low PAPR is not an inherent guarantee; measure against DFT-s-OFDM using the actual pulses and amplifier. |
| Receiver effort and latency | Mature FFT-based processing; advanced ICI mitigation adds cost. | Mature processing with additional spreading/despreading. | Joint estimation/detection may add iterations, memory, compute, and block-buffering latency. |
| Net spectral efficiency | Depends on CP, pilots, control, coding, and retransmissions. | Depends on the same overheads and resource mapping. | Fewer retransmissions may help, but pilots, guards, CP, and detection failures can erase gains. |
| Scheduling and MIMO | Established time–frequency scheduling and MIMO ecosystem. | Resource mapping and spatial-processing choices affect flexibility. | Delay–Doppler scheduling, channel acquisition, multiuser interference, and scalable MIMO need validation. |
| Sensing / non-terrestrial links | Also supports sensing research and Doppler compensation. | Suitability depends on the system design. | Delay–Doppler processing is attractive for sensing and residual time variation, but does not by itself solve synchronization, propagation delay, or interference. |
| Deployment maturity | Established NR baseline [2]. | Established NR uplink option [2]. | Additional processing may reuse parts of OFDM implementations; this does not establish over-the-air interoperability or commercial readiness. |

**Where OTFS is most interesting:** high-speed rail, vehicular links, and
non-terrestrial links with challenging *residual* Doppler after compensation.
Carrier frequency, speed, delay spread, Doppler spread, and scheduling must be
specified: high speed alone is not proof that OFDM fails. A large common Doppler
shift is also different from Doppler spread and may be compensated.

**Where the business case may be weak:** stationary or low-mobility traffic,
short packets with tight latency budgets, and uplinks where terminal power
efficiency dominates. Existing numerology, channel tracking, coding, beamforming,
or ICI mitigation may solve the problem more economically.

## 3. Operator benefits: hypotheses to measure

| Potential benefit | Required evidence | Cost or risk to include |
| --- | --- | --- |
| More reliable service on moving routes | Lower outage, BLER, and retransmissions at equal resources and practical channel estimates | Receiver complexity, acquisition failures, and coverage gaps unrelated to the waveform |
| Better cell-edge service or fewer capacity upgrades | Higher delivered goodput and lower required SINR across the target traffic mix | No automatic site reduction: link budget, interference, and coverage obligations still apply |
| Lower energy per delivered bit | Measured UE and network joules per successfully delivered payload bit | Amplifier back-off, extra processing, memory, and retransmission energy |
| New mobility or sensing services | Demonstrated service KPI and willingness to pay | Device availability, sensing/privacy obligations, and integration costs |
| Reuse of existing investments | Measured feasibility on representative radio/baseband hardware | Software-only migration must not be assumed; RF, memory, accelerators, and interoperability may change |

Evaluate total cost of ownership (TCO), not just simulated BER:

`Incremental value = avoided capacity/energy/service-failure costs + incremental service margin − incremental deployment and operating costs`.

Use the operator's measured traffic mix, equipment lifetimes, and rollout scale.
If the benefit applies only to a small mobility segment, do not extrapolate it
to the whole network. Link-level improvement alone does not fix handovers,
backhaul congestion, or transport/application latency.

## 4. Stakeholder and expert perspectives

### Published technical perspectives

- **OTFS proponents:** Hadani et al.'s 2017 proposal [1] argues for delay–Doppler
  modulation to address time-varying channels and exploit diversity. This is the
  inventors' technical case, not an independent operator endorsement.
- **Receiver researchers:** Raviteja et al.'s 2018 work on interference
  cancellation and iterative OTFS detection [3] provides a practical algorithmic
  perspective: detection and interference management matter. One algorithm or
  simulation is not evidence of deployment-wide superiority.
- **Standards context:** NR's waveform specification [2] supplies the legacy
  baseline. ITU-R M.2160-0 [4] supplies the IMT-2030 framework and objectives,
  including usage scenarios such as integrated sensing and communication.
  Neither reference should be treated as an endorsement of OTFS for 6G.

These are paraphrased technical perspectives, not interview quotations or a
claim of expert consensus. Source retrieval and independent assessment remain
open work.

### Stakeholder questions, not attributed opinions

No operator, vendor, chipset maker, or regulator was interviewed for this study.
The positions below are **assessment hypotheses to validate**, not statements
made by those organizations.

| Stakeholder | Likely decision criterion | Evidence to request |
| --- | --- | --- |
| Mobile operator | Service differentiation, coverage, energy, TCO, and backward coexistence | Route-level trials, traffic-weighted economics, and migration plan |
| Radio/baseband vendor | Implementability, MIMO scale, throughput, and hardware reuse | Real-time prototype with compute, memory, power, and latency measurements |
| UE/chipset maker | Battery life, silicon area, thermal limits, and transmit efficiency | Hardware measurements against DFT-s-OFDM uplink, not only CP-OFDM |
| Standards bodies / regulators | Interoperability, spectral coexistence, and testable requirements | Dated specifications/contributions and emission/coexistence tests |
| Independent researchers | Reproducible gains and realistic assumptions | Matched baselines, public parameters, practical channel estimates, and independent replication |

Seek dated public operator positions through sources such as NGMN's publications
catalogue [5], then record the exact publication, author/organization, date,
use case, and evidence. General support for efficient 6G is **not** support for
OTFS. Vendor advocacy, simulation results, prototype demonstrations, standards
proposals, and adopted specifications must be recorded separately.

## 5. A staged 6G investigation and adoption strategy

1. **Identify the unmet operator need.** Select a specific route/service and
   baseline failure mode. Set acceptance thresholds before testing: net
   goodput, outage, tail latency, energy per delivered bit, and incremental TCO.
   Tie these to relevant IMT-2030 objectives [4], not a waveform preference.
2. **Build a fair, reproducible link comparison.** Include CP-OFDM and, for
   uplink, DFT-s-OFDM. Fix bandwidth, carrier, allocated time/resources, payload,
   coding, antenna configuration, transmit-power limits, and channel realizations.
   Document numerology, pulse shape, frame duration, and all CP/pilot/guard/control
   overhead. Also compare optimized baselines under the same resource and
   latency budgets; do not handicap OFDM with an unsuitable numerology.
3. **Separate ideal from practical gains.** Run perfect-channel-knowledge cases
   only as bounds. Test practical channel estimation, integer and fractional
   Doppler, sparse and diffuse multipath, synchronization error, phase noise,
   RF nonlinearity, interference, and MIMO. Include low-mobility and short-packet
   cases to expose regressions, and residual Doppler after realistic compensation.
4. **Account for implementation cost.** Report BER/BLER, payload goodput,
   retransmissions, outage, latency distributions, PAPR distribution, amplifier
   efficiency, compute/iteration limits, memory, and UE/network energy. Include
   estimation, buffering, decoding, and HARQ time in end-to-end results. State
   seeds, sample counts, uncertainty, and detector stopping rules.
5. **Prototype, then trial.** First demonstrate real-time operation on
   representative hardware. Then use controlled over-the-air tests and target
   routes, including multiuser scheduling, mobility, coexistence, and handovers.
   Repeat across representative loads and environments, with independent review.
6. **Apply a go/no-go gate.** Advance only if pre-agreed service gains survive
   practical receivers and yield positive incremental value without unacceptable
   battery, latency, coexistence, or interoperability regressions. Otherwise
   retain the legacy waveform for that service.
7. **Consider limited introduction before broad replacement.** Subject to
   standards and device support, evaluate an optional mode for a justified use
   case with capability negotiation and legacy fallback. Assess spectrum masks,
   certification, essential-IP/licensing exposure, and vendor diversity.
   OFDM-based implementation reuse alone does not authorize an interoperable
   rollout.

The next deliverable should be a reproducible experiment specification and a
dated stakeholder evidence register. A universal 6G waveform recommendation
would be premature without those results.

## References and evidence limits

The following are literature/standards pointers, **not sources successfully
retrieved in this investigation**. Verify the documents and their applicability
before using this study for procurement or standards decisions.

1. R. Hadani et al., *Orthogonal Time Frequency Space Modulation*, IEEE WCNC,
   2017. <https://doi.org/10.1109/WCNC.2017.7925924>.
   Foundational proposal; not commercial deployment evidence.
2. 3GPP TS 38.211, *NR; Physical channels and modulation*.
   <https://www.3gpp.org/dynareport/38211.htm>.
   Versioned NR specification; select and record a release/version for experiments.
3. P. Raviteja et al., *Interference Cancellation and Iterative Detection for
   Orthogonal Time Frequency Space Modulation*, IEEE Transactions on Wireless
   Communications, 2018. <https://doi.org/10.1109/TWC.2018.2860011>.
   Receiver research; results depend on the modeled assumptions.
4. ITU-R Recommendation M.2160-0, *Framework and overall objectives of the future
   development of IMT for 2030 and beyond*, November 2023.
   <https://www.itu.int/rec/R-REC-M.2160-0-202311-I/en>.
   Framework, not a waveform selection or a substitute for evaluation criteria.
5. NGMN, publications catalogue: <https://www.ngmn.org/publications.html>.
   Follow-up lead for operator perspectives only; no OTFS endorsement is inferred.
