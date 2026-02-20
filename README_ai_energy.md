# Photonic Interconnects as a Scaling Lever
### Baseline-Free Scenario Screening for AI Data Center Power, GPU Capacity, and CO₂ Emissions

**Author:** Dr. Boris Kiefer · [github.com/boriskiefer](https://github.com/boriskiefer) · [linkedin.com/in/boris-kiefer-85089831](https://linkedin.com/in/boris-kiefer-85089831)

---

## Overview

AI data centers face orthogonal constraints — power delivery, cooling, carbon,
water, and grid interconnection — whose relative severity is scenario-dependent.
This repository provides a **bottleneck-first screening framework** and a
**transparent, baseline-free sensitivity model** that quantifies the impact of
photonic interconnects as an indirect scaling lever on facility MW, GPU capacity,
and CO₂ emissions.

The analysis is structured as a Solution Architecture engagement:

| Stage | Content |
|---|---|
| **Customer** | AI infrastructure operators facing scaling constraints |
| **Gap** | Dominant bottleneck varies by scenario; no single lever fits all deployments |
| **Solution** | Bottleneck/lever map + photonic gain model parameterized by `g` |
| **Deliver** | Executive brief (PDF) + reproducible Python simulator |

---

## Repository Contents

```
/
  README.md                             — this file
  Tier01_AI_energy_brief.pdf            — executive brief
  Tier01_ai_energy_tier0_022026.ipynb   — simulator (edit header, run)
  requirements.txt                      — Python dependencies
/
```

---

## Simulator

`Tier01_ai_energy_tier0_022026.ipynb` is a **modify-header-and-run** Jupyter
notebook — no command-line arguments, no interactive widgets. All scenario
parameters are defined in a single `USER-EDITABLE DEFAULTS` cell at the top
of the notebook. Edit the values and run all cells:

```bash
jupyter lab Tier01_ai_energy_tier0_022026.ipynb
```

Then: **Kernel → Restart Kernel and Run All Cells**.

All plots and CSV tables are written to a `outputs/` directory.

### Key parameters to edit

| Parameter | Meaning | Default |
|---|---|---|
| `dc_sweep_facility_mw_max` | Max baseline facility power in sweep [MW] | 1000 |
| `dc_indirect_g_scenarios` | Scaling gain values to compare | [0.00, 0.05, 0.10, 0.15, 0.20, 0.25] |
| `dc_site_pue_for_sizing` | Power usage effectiveness | 1.25 |
| `dc_it_frac_gpu_equiv` | GPU fraction of IT power | 0.45 |
| `dc_w_per_gpu_equiv` | Watts per GPU-equivalent [W] | 1275 |
| `ex_electricity_cost_per_kwh` | Electricity cost [$/kWh] | 0.10 |
| `ex_co2_plot_factor` | CO₂ scenario: `"gas"` or `"grid"` | `"gas"` |
| `dc_sens_baseline_facility_mw` | Tornado baseline facility size [MW] | 500 |
| `dc_sens_g` | Tornado baseline scaling gain | 0.10 |

### Outputs

**Plots (PNG):**
- `plot_g_percent_saved.png` — savings (%) vs. scaling gain `g`
- `plot_MW_saved_vs_facilityMW.png` — MW avoided across facility size and `g`
- `plot_GPU_saved_vs_facilityMW.png` — GPU-equivalents avoided
- `plot_CO2_saved_vs_facilityMW.png` — CO₂ avoided (metric tonnes/yr)
- `plot_cost_saved_vs_facilityMW.png` — annual electricity cost savings ($M/yr)
- `plot_facilityMW_after_vs_facilityMW.png` — required capacity after applying `g`
- `plot_tornado_MW_saved.png` — OAT sensitivity: MW avoided
- `plot_tornado_GPU_saved.png` — OAT sensitivity: GPU-equivalents avoided
- `plot_tornado_CO2_saved.png` — OAT sensitivity: CO₂ avoided
- `plot_tornado_direct_network_MW_saved.png` — OAT sensitivity: direct network MW

**CSV tables:**
- `g_percent_savings.csv` — savings fractions by `g`
- `facility_sweep_savings.csv` — full sweep results
- `sensitivity_tornado.csv` — OAT sensitivity results

---

## Core Model

The photonic lever `g` (effective scaling gain from improved interconnect
or system behavior) converts directly to saved fraction:

```
Saved fraction  =  g / (1 + g)
P_after         =  P₀ / (1 + g)
```

If baseline effective utilization is `u₀` and an intervention raises it
to `u₁`, then `g ≈ u₁/u₀ − 1` for workloads where lost utilization is
predominantly due to coordination/communication limits.

**Literature-based range for `g`:**

| Value | Rationale |
|---|---|
| 0.00 | Null case — compute-bound deployment, interconnect not the bottleneck |
| 0.05 | Conservative — consistent with measured contention mitigation gains |
| 0.10 | Plausible mid-case — meaningful collective/communication overhead |
| 0.20 | Upper bound — consistent with Crux (SIGCOMM'24) trace simulation results (~23%) |

---

## Disclaimer

This assessment provides a transparent, baseline-free sensitivity model.
It does not provide a Jupiter-calibrated digital twin and does not claim
interconnect is the dominant bottleneck for any specific deployment.
Follow-on studies should estimate effective scaling gain from workload
traces, scheduler behavior, and fabric telemetry.

---

## SA Methodology Note

This repository demonstrates an AI-augmented solution architecture workflow:
customer-first problem framing, gap identification, working POC delivery,
and explicit hand-off documentation. Built using domain-expert
human-in-the-loop guidance with LLM assistance.

*See also: [Stern-Gerlach Quantum Simulator](https://github.com/boriskiefer/stern-gerlach-poc)*
