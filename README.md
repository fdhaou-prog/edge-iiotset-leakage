# Correlation-Based Leakage Screening Is Insufficient for IoT/IIoT Intrusion-Detection Benchmarks: Redundant Shortcut Encoding in Edge-IIoTset

Reproducibility package for the study of data leakage and shortcut learning in the **Edge-IIoTset** IoT/IIoT intrusion-detection benchmark.

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.21328602.svg)](https://doi.org/10.5281/zenodo.21328602)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

> **Summary.** On Edge-IIoTset, a linear classifier attains *perfect* binary separation (accuracy 1.0000) of fourteen heterogeneous attack types from benign traffic — a shortcut, not a genuine result. A correlation screen flags four protocol-identity fields carrying it; removing them collapses the linear model (1.0000 → 0.9566) but leaves Random Forest classifying all but one of 30,449 held-out records correctly (a single false positive). Mutual information, recursive feature elimination, an ablation cascade, adversarial validation, repeated cross-validation and a 1D-CNN show the shortcut is redundantly encoded and that **correlation-based screening is necessary but not sufficient**. Applied to CICIoT2023, the same protocol does not reproduce the signature, showing it discriminates leakage-driven from signal-driven benchmarks.

---

## Repository contents

| File | Description |
|------|-------------|
| `01_main_analysis.ipynb` | Preprocessing, correlation leakage screen, binary + multi-class classification, SHAP. Produces the core results (Tables 2, 3, 8, 9). |
| `02_encoding_robustness.ipynb` | Reproduces the Logistic Regression collapse under label vs one-hot encoding, training-partition-only (Section IV-C). |
| `03_revision_experiments.ipynb` | Mutual information, recursive feature elimination, adversarial validation, feature-ablation cascade, near-duplicate screen, repeated stratified cross-validation, 1D-CNN, extended imbalance-robust metrics, correlation-threshold sensitivity (Sections IV-D to IV-G; Tables 4, 6, 7). |
| `04_cross_dataset_ciciot2023.ipynb` | Applies the diagnostic protocol to CICIoT2023; shows the Edge-IIoTset signature is absent (Section V). |
| `05_figures.ipynb` | Regenerates all six manuscript figures at 300 dpi from the same pipeline. |
| `requirements.txt` | Python dependencies. |
| `CITATION.cff` | Citation metadata. |
| `LICENSE` | MIT License. |

## Which cell produces which figure (`05_figures.ipynb`)

| Cell | Output file | Manuscript |
|------|-------------|------------|
| 3 | `Figure1_ROC.png` | Figure 1 — ROC curves (leakage-controlled) |
| 4 | `Figure2_binary_confusion.png` | Figure 2 — Binary confusion matrix (one false positive) |
| 5 | `Figure3_ablation.png` | Figure 3 — Feature-ablation cascade |
| 6 | `Figure4_multiclass_confusion.png` | Figure 4 — Multi-class confusion matrix (15 classes) |
| 7 | `Figure5_feature_importance.png` | Figure 5 — Top-15 Gini feature importance |
| 8 | `Figure6_shap_summary.png` | Figure 6 — SHAP summary (n = 1000) |

## Data

The notebooks require the ML-ready CSV files, which are **not** redistributed here:

- **Edge-IIoTset** — `ML-EdgeIIoT-dataset.csv` (157,800 × 63). Available from the original publishers (Ferrag et al., IEEE Access 2022) via IEEE DataPort / Kaggle.
- **CICIoT2023** — `train.csv` (Neto et al., Sensors 2023). Available from the Canadian Institute for Cybersecurity (UNB) / Kaggle. Used only by notebook 04.

Place the relevant CSV in the working directory (or the Colab Files panel) before running a notebook.

## How to run

**Google Colab (recommended):** open a notebook via *File → Open notebook → GitHub*, enter `fdhaou-prog`, select the notebook, upload the CSV to the Files panel, and run all cells in order.

**Locally:**
```bash
pip install -r requirements.txt
jupyter notebook
```

All experiments use a fixed random seed (`RS = 42`). After the 13 identifier/payload fields are dropped and duplicates removed, every notebook asserts the intermediate record count (152,245), so any deviation from the published pipeline is detected rather than silently propagated.

## Key results reproduced

- Linear classifier: perfect binary separation on the uncontrolled feature set (accuracy 1.0000).
- Four flagged protocol-identity fields: `dns.qry.name.len` (r ≈ 0.967), `mqtt.protoname`, `mqtt.topic`, `mqtt.conack.flags` (r ≈ 0.877).
- After control: Random Forest 1.0000 (single false positive), Logistic Regression 0.9566.
- Ablation floor ≈ 0.84 (conservative estimate of genuine signal).
- Adversarial validation AUC = 0.4959 (clean split); near-duplicate rate 0.47%.
- CICIoT2023: max |r| = 0.53, balanced linear separation MCC = 0.68 (no Edge-IIoTset signature).

## Citation

If you use this code, please cite the paper (see `CITATION.cff`) and this archive:

> F. M. Dhaou, *Correlation-Based Leakage Screening Is Insufficient for IoT/IIoT Intrusion-Detection Benchmarks: Redundant Shortcut Encoding in Edge-IIoTset.* Code archive, Zenodo, https://doi.org/10.5281/zenodo.21328602

## License

MIT — see [LICENSE](LICENSE).
