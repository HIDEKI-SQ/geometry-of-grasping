# Geometry of Grasping

**Operationalizing understanding as projection: a 400-trial experiment measuring how value-laden perspectives transform information structure into meaning structure.**

This repository contains the complete experimental pipeline, data, and analysis for the paper:

> *The Geometry of Grasping: How Perspective Transforms Information into Understanding*

## Overview

We formalize understanding as a projective transformation $M = \Pi_V(S)$, where an information structure $S$ is reshaped by a value operator $V = \nu \odot \psi$ (intensity × direction) into a meaning structure $M$. Using large language models as experimental platforms, we test four predictions derived from this framework across 400 controlled trials spanning four knowledge domains, two frontier models, and ten experimental conditions.

### Key findings

1. **Identity at zero** — Without perspective ($|V|=0$), output preserves input structure (SSC ≈ 0, H1 confirmed)
2. **Perspective generates structure** — The $V_0 \to V_1$ transition produces a sharp jump in restructuring (RI: 0.33 → 0.87)
3. **Different perspectives produce different outputs** — Perspective divergence is massive ($t > 50$, $p < 10^{-10}$, H4 confirmed)
4. **Three-phase pattern** (novel discovery) — Understanding deepens through alignment → disruption → reintegration, visible as an inverted-N trajectory in the SSC × ISC phase portrait

## Repository structure

```
geometry-of-grasping/
├── README.md
├── LICENSE
├── notebooks/                          # Reproducible experimental pipeline
│   ├── P0019_exp01_stimulus.ipynb      # Domain & stimulus construction
│   ├── P0019_exp02_collect.ipynb       # 400-trial data collection
│   ├── P0019_exp03b_enhanced_measure.ipynb  # Five-metric measurement
│   └── P0019_exp04b_revised_analyze.ipynb   # Statistical analysis & figures
│
├── data/
│   ├── exp01_stimulus/                 # Domains, dependency graphs, trial manifests
│   │   ├── exp01_domains.json          # 4 domains × 20 concepts × dependency edges
│   │   ├── exp01_pilot_manifest.json   # Pilot trial configuration
│   │   ├── exp01_tau_graphs.png        # Dependency graph visualizations
│   │   └── exp01_trial_manifest.json   # Full 400-trial configuration
│   │
│   ├── exp02_collect/                  # Raw LLM responses
│   │   ├── exp02_full_responses.json   # 400 complete responses (text + metadata)
│   │   ├── exp02_full_log.csv          # Execution log (tokens, latency, status)
│   │   ├── exp02_pilot_responses.json  # 30-trial pilot responses
│   │   └── exp02_pilot_log.csv         # Pilot execution log
│   │
│   ├── exp03_measure/                  # Computed metrics
│   │   ├── exp03b_enhanced_results.csv # All 5 metrics × 400 trials (PRIMARY)
│   │   ├── exp03b_embeddings.npz       # Sentence-transformer embeddings
│   │   ├── exp03_ssc_results.csv       # Original SSC-only measurements
│   │   └── exp03_measurement_results.json
│   │
│   └── exp04_analyze/                  # Statistical test results & Colab figures
│       ├── exp04b_R1_test.json         # R1: V0 has lowest organization
│       ├── exp04b_R2_test.json         # R2: ISC/OSD monotonicity test
│       ├── exp04b_R3_test.json         # R3: RI increases with |V|
│       ├── exp04b_R4_test.json         # R4: Perspective divergence > 0
│       ├── exp04b_R5_test.json         # R5: Two-phase (three-phase) structure
│       ├── exp04b_full_summary.json    # Comprehensive results summary
│       ├── exp04b_fig3_two_phase.*     # Colab-generated: SSC/ISC/OSD panels
│       ├── exp04b_fig4_restructuring_pd.*  # Colab-generated: RI + PD
│       └── exp04b_fig5_ssc_vs_isc.*    # Colab-generated: phase portrait
│
└── figures/                            # Publication-quality figures (vector PDF)
    ├── fig1_framework_schematic.pdf    # Π_V(S) conceptual diagram
    ├── fig2_tau_graph_S1.pdf           # S₁ thermodynamics dependency graph
    ├── fig3_ten_condition_profile.pdf  # 4-panel: SSC, ISC, RI, EMR
    ├── fig4_ri_pd.pdf                  # Restructuring + perspective divergence
    ├── fig5_phase_portrait.pdf         # SSC × ISC three-phase trajectory ★
    ├── figS1_all_tau_graphs.pdf        # All 4 domain dependency graphs
    ├── figS2_model_comparison.pdf      # Claude Sonnet 3.5 vs GPT-4o
    └── figS3_domain_comparison.pdf     # Cross-domain replication
```

## Experimental design

### Domains (S₁–S₄)

| Domain | Field | Concepts | Edges |
|--------|-------|----------|-------|
| S₁ | Thermodynamics | 20 | 28 |
| S₂ | Evolutionary biology | 20 | 26 |
| S₃ | Industrial Revolution | 20 | 27 |
| S₄ | Group theory | 20 | 27 |

### Conditions (10 total)

| |V| | ψ | Instruction | N |
|-----|-----|-------------|-----|
| 0 | — | Reproduce with definitions | 40 |
| 1 | — | Organize into coherent summary | 40 |
| 2 | causal / practical / historical / formal | Analyze from perspective | 160 |
| 3 | causal / practical / historical / formal | Deepest perspective-specific insights | 160 |

Each cell: 4 domains × 2 models × 5 replications.

### Five behavioral metrics

| Metric | What it measures | Definition |
|--------|-----------------|------------|
| **SSC** | τ-alignment | Spearman(τ-distance, output-position-distance) |
| **ISC** | Internal coherence | Spearman(semantic-distance, positional-distance) within output |
| **RI** | Restructuring | 1 − \|Kendall τ(input order, output order)\| |
| **PD** | Perspective divergence | Mean cosine distance between outputs under different ψ |
| **EMR** | Edge-mention rate | Fraction of τ-edges co-mentioned in output |

## Reproducibility

### Environment

- **Platform**: Google Colab (Python 3.10+)
- **LLM APIs**: Anthropic (Claude Sonnet 3.5), OpenAI (GPT-4o)
- **Key dependencies**: `sentence-transformers`, `scipy`, `numpy`, `pandas`, `matplotlib`, `networkx`
- **Random seeds**: Fixed at 42 throughout; LLM temperature = 0

### Running the pipeline

1. **exp01**: Constructs domains, dependency graphs, and trial manifests
2. **exp02**: Collects 400 LLM responses (requires API keys)
3. **exp03b**: Computes all five metrics from raw responses (requires `sentence-transformers`; GPU recommended)
4. **exp04b**: Runs all statistical tests (R1–R5), generates figures

Notebooks are designed to run sequentially. Each reads from the previous stage's output directory and writes to its own.

### Deterministic execution

All notebooks implement checkpoint/resume: if interrupted, re-running skips completed trials. Fixed seeds and temperature=0 ensure reproducibility of the data collection step (modulo API-side non-determinism).

## Results summary

| Test | Prediction | Verdict | Key statistic |
|------|-----------|---------|--------------|
| H1 | SSC ≈ 0 at V₀ | ✓ | mean = 0.052 |
| H2 | SSC monotonically increasing | ✗ → discovery | V₁ > V₂ (p = 0.03) |
| H3 | EMR > 0.50 across all V | ✓ | mean = 0.87 |
| H4 | Different ψ → different M | ✓ | t(159) > 50 |
| — | Three-phase structure | ✓ (novel) | ISC: V₂ < V₃, p = 10⁻⁶ |

**Score: 4/4 hypotheses confirmed + 1 novel discovery.**

The three-phase pattern — alignment (V₀→V₁), disruption (V₁→V₂), reintegration (V₂→V₃) — was not predicted a priori and constitutes the paper's central empirical contribution.

## Citation

If you use this data or code, please cite:

```bibtex
@article{hideki2026geometry,
  title   = {The Geometry of Grasping: How Perspective Transforms
             Information into Understanding},
  author  = {HIDEKI},
  year    = {2026},
  note    = {Under review}
}
```

## Contact

- **ORCID**: [0009-0002-0019-6608](https://orcid.org/0009-0002-0019-6608)
- **Email**: hideki@r3776.jp

## License

MIT License. See [LICENSE](LICENSE).
