# Phase 4 — Research Paper Writing

**Lightweight Open-Set Intrusion Detection for Encrypted-Port Network Flows: Unseen Attacks, CPU Cost, Cross-Dataset Transfer and Explainability**

Romaisa (24I-0061) · Maham Anjum (24I-0053)
Department of Artificial Intelligence and Data Science, FAST-NUCES Islamabad
Computer Networks Research Project, Section AI-5C. Instructor: Ms. Laraib

This folder contains the final research paper, which turns the work from Phases 1–3 into a journal-style manuscript using the Springer Nature LaTeX template (two-column layout, 12 pages including references, plus appendices).

## Contents

| Path | Description |
|------|-------------|
| `24i-0053_24i-0061_Phase4_Research_Paper.pdf` | Compiled research paper |
| `latex/main.tex` | Manuscript source |
| `latex/references.bib` | Bibliography (BibTeX) |
| `latex/figs/` | Figures produced by the Phase 3 experiments |
| `latex/sn-jnl.cls`, `latex/*.bst` | Springer Nature template class and reference styles |

## Compiling the paper

**Overleaf:** zip the `latex/` folder, choose *New Project → Upload Project*, set `main.tex` as the main document and compile with pdfLaTeX.

**Locally:**
```bash
cd latex
pdflatex main.tex
bibtex main
pdflatex main.tex
pdflatex main.tex
```

## Paper structure

1. Introduction: problem, research gap (G1, G2, G3, G5) and contributions
2. Related Work: thematic review and summary table of 13 studies
3. Proposed Methodology: problem formulation, architecture, MLP encoder, prototype open-set head, explanations, recalibration, pseudocode and changes after Phase 2
4. Experimental Setup: datasets, cleaning, temporal splits, protocols A/B/C/E, baselines, metrics and implementation
5. Results and Analysis: known attacks, unseen families, cross-dataset transfer, CPU cost, explainability and error analysis
6. Comparison with Base Papers: OHCE, DC-GL, ET-SSL, EVADE and GCN-MHA
7. Ablation Study
8. Discussion: why the method works, limitations and threats to validity, gap achievement
9. Conclusion and Future Work
10. Declarations (data and code availability, funding, conflicts of interest, AI-tool use) and References

## Key results

All results come from the Phase 3 experiments (see `../Phase-03`), on 241,109 cleaned encrypted-port flows from CIC-IDS-2017 and CSE-CIC-IDS2018, with temporal splits, validation-only thresholds at 1% FPR and five seeds.

| Finding | Result |
|---------|--------|
| Known attacks (Protocol A) | AUROC 0.922 (XGBoost 0.950); realised FPR 5.5% vs. 15.5% for XGBoost; F1 0.679 vs. 0.651 |
| CPU cost (Protocol E) | 0.38 ms per flow on one thread, 7,092 parameters, 0.03 MB; 21× faster than Random Forest, 3.1× faster than XGBoost |
| Cross-dataset transfer (Protocol C) | Recalibration on 1,000 unlabeled benign flows restores 1.11% / 1.02% FPR (3 of 4 criteria met) |
| Explainability | Integrated Gradients is faithful (top-10 deletion removes all unseen alerts vs. 35% for random) and costs 1.47 ms (p95) |
| Unseen families (Protocol B) | Not detected by any method at 1% FPR |
| Evaluation protocol | A random split raises Random Forest accuracy from 85.2% to 95.9% |

## Research gaps addressed

| Gap | Status |
|-----|--------|
| G1: unseen attack families | Not achieved |
| G2: CPU cost | Achieved |
| G3: cross-dataset transfer | Partly achieved |
| G5: explainability of alerts | Partly achieved |

## Reproducibility

- **Data:** CIC-IDS-2017 and CSE-CIC-IDS2018 (public Kaggle releases); flows on ports 22, 443, 465, 993, 995 and 8443
- **Split:** 70/15/15 by time within each (dataset, family) group
- **Seeds:** 0–4 for main results, 0–2 for ablations
- **Hardware:** Intel Core i5-8365U (4 cores, 8 threads), 8 GB RAM, Windows
- **Software:** Python 3.13.9, NumPy 2.4.6, pandas 3.0.6, scikit-learn 1.9.1, XGBoost 3.4.1, PyTorch 2.13.0, SHAP 0.54.1
- **Code:** see `../Phase-03` for the implementation notebook