# AI-Based Intrusion Detection for Encrypted Network Traffic

A lightweight intrusion detection system for encrypted network traffic. It combines a compact neural network, prototype-based open-set scoring, temporal evaluation, cross-dataset transfer and explainable AI.

**Romaisa** (24I-0061) · **Maham Anjum** (24I-0053)
Department of Artificial Intelligence and Data Science, FAST-NUCES Islamabad
Computer Networks Research Project, Section AI-5C. Instructor: Ms. Laraib

---

## Overview

Encryption (TLS, SSH, QUIC) hides packet payloads, so an intrusion detection system must work from flow metadata: packet sizes, timing, TCP flags and volume. Published encrypted-traffic detectors report 94–99% accuracy, but most are tested with random splits, on GPUs and on a single dataset, without explaining their alerts.

This project builds and evaluates a detector that targets four research gaps together:

| Gap | Question | Outcome |
|-----|----------|---------|
| G1 | Can it detect attack families never seen in training? | Not achieved |
| G2 | Is it cheap enough to run on a CPU? | Achieved |
| G3 | Does it transfer to a different network/dataset? | Partly achieved |
| G5 | Can its alerts be explained faithfully? | Partly achieved |

## Repository structure

```
AI-Intrusion-Detection-Encrypted-Traffic/
├── README.md                     ← this file
├── Phase-01/
│   └── Phase1.pdf                ← Initial literature review and research gaps
├── Phase-02/
│   └── Phase2.pdf                ← Extended literature review and research methodology
├── Phase-03/
│   ├── 24i-0053_24i-0061_Phase3_Implementation.ipynb   ← Full implementation and experiments
│   └── 24i-0053_24i-0061_Phase3_Report.pdf             ← Implementation and evaluation report
└── Phase-04/
    ├── README.md                                   ← Phase 4 details
    ├── 24i-0053_24i-0061_Phase4_Research_Paper.pdf ← Final research paper
    └── latex/                                      ← Springer Nature LaTeX source and figures
```

## Project phases

**Phase 1: Initial literature review.** A review of three 2026 Q1 papers (Adaptive Feature Selection, FIRE-G, MTDecipher) on encrypted malicious-traffic detection, which found four gaps: cross-environment detection, unseen attacks, computational cost and realistic traffic variation.

**Phase 2: Extended literature and methodology.** A comparison of ten 2025–2026 papers (OHCE, DC-GL, ET-SSL, E-MGFlow, EVADE, HERO, Federated CIL, path signatures, a graph framework and GCN-MHA). It refines the gaps into G1–G5 and designs a 12-step research methodology.

**Phase 3: Implementation and evaluation.** Builds the detector and evaluates it against seven re-implemented baselines under four protocols, with five seeds, confidence intervals, significance tests, ablations and error analysis.

**Phase 4: Research paper.** Turns Phases 1–3 into a 12-page journal-style paper in the Springer Nature template. See [`Phase-04/README.md`](Phase-04/README.md).

## Method in brief

- **Data:** CIC-IDS-2017 and CSE-CIC-IDS2018, filtered to encrypted/secure ports (22, 443, 465, 993, 995, 8443), cleaned of 470,510 duplicates and 23,262 label conflicts, giving 241,109 flows (benign, SSH brute force, PortScan, Infiltration).
- **Splits:** 70/15/15 by time within each (dataset, family), with no random splits.
- **Detector:** an MLP encoder (68 → 64 → 32 → 16, 7,092 parameters) with a prototype-based open-set head. The score is the maximum of a calibrated benign-boundary distance and a benign-vs-attack prototype ratio.
- **Thresholds:** set only on benign validation flows for a 1% false-positive rate.
- **Baselines:** Random Forest, XGBoost, Isolation Forest, an EVADE-style Elliptic Envelope, MSP, Mahalanobis and MLP-closed.
- **Protocols:** A (closed-set), B (leave-one-family-out), C (cross-dataset) and E (single-thread CPU cost).
- **Explanations:** Integrated Gradients and Expected Gradients computed on the detector's own score.

## Key results

| Finding | Result |
|---------|--------|
| Known attacks | AUROC 0.922 (XGBoost 0.950); realised FPR 5.5% vs. 15.5% for XGBoost; precision 0.710 vs. 0.529 |
| CPU cost | 0.38 ms per flow, 0.03 MB; 21× faster than Random Forest, 3.1× faster than XGBoost |
| Transfer to a new network | Recalibration on 1,000 unlabeled benign flows restores 1.11% / 1.02% FPR |
| Explanations | Faithful (top-10 deletion removes all unseen alerts vs. 35% for random); 1.47 ms (p95) |
| Unseen attack families | Not detected by any method at 1% FPR |
| Evaluation protocol | A random split raises Random Forest accuracy from 85.2% to 95.9% |

## Running the code

1. **Get the data.** Download the CIC-IDS-2017 CSVs (`*_ISCX.csv`, 8 files) and the CSE-CIC-IDS2018 CSVs (10 files) from the Canadian Institute for Cybersecurity or their Kaggle releases.
2. **Install dependencies** (Python 3.13 was used):
   ```bash
   pip install numpy pandas scikit-learn xgboost shap pyarrow tabulate seaborn matplotlib
   pip install torch --index-url https://download.pytorch.org/whl/cpu
   ```
3. **Configure.** Open `Phase-03/24i-0053_24i-0061_Phase3_Implementation.ipynb` and, in the configuration cell, set `DATA_2017_DIR`, `DATA_2018_DIR` and `OUT_DIR`.
4. **Run top to bottom.** `QUICK_MODE = True` gives a 10-minute test run. The reported results use `QUICK_MODE = False` (5 seeds, about 2.5–3 hours). Each stage saves to `OUT_DIR`, so a crashed run can resume.

**Reported environment:** Windows laptop, Intel Core i5-8365U (4 cores, 8 threads), 8 GB RAM, Python 3.13.9, NumPy 2.4.6, pandas 3.0.6. Latency is measured on one CPU thread.

## Citation

> Romaisa and M. Anjum, "Lightweight Open-Set Intrusion Detection for Encrypted-Port Network Flows: Unseen Attacks, CPU Cost, Cross-Dataset Transfer and Explainability," Computer Networks Research Project, FAST-NUCES Islamabad, 2026.