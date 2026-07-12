# Correlation-Based Leakage Screening Is Insufficient for IoT/IIoT Intrusion-Detection Benchmarks

Reproducibility package for the manuscript:

> F. M. Dhaou, "Correlation-Based Leakage Screening Is Insufficient for IoT/IIoT Intrusion-Detection Benchmarks: Redundant Shortcut Encoding in Edge-IIoTset," submitted to *IEEE Access*, 2026.

**Author:** Fatma Mohammed Dhaou, Department of Management Information Systems, Faculty of Business Administration, University of Tabuk, Saudi Arabia
**ORCID:** [0009-0000-9028-8033](https://orcid.org/0009-0000-9028-8033)
**Contact:** fdhaou@ut.edu.sa

---

## What this repository contains

Code to reproduce every table and figure in the manuscript, from the raw Edge-IIoTset CSV to the final SHAP attributions.

| File | Purpose |
|---|---|
| `01_main_analysis.ipynb` | Full pipeline: preprocessing, leakage screen, four classifiers on both feature sets, multi-class evaluation, SHAP. Reproduces Tables 2, 3, 5, 6 and Figures 1–5. |
| `02_encoding_robustness.ipynb` | Robustness check: Logistic Regression under label vs. one-hot encoding, all preprocessing fitted on the training partition only. Reproduces Table 4. |
| `requirements.txt` | Pinned dependencies. |
| `LICENSE` | MIT. |

Both notebooks run without modification in Google Colab.

---

## The finding, in brief

On the uncontrolled 48-feature set, an L2-regularised **linear** classifier separates fourteen structurally heterogeneous attack types from benign traffic with **zero errors**. No genuine detection problem should permit this, and it indicates a label-encoding shortcut.

A univariate correlation screen identifies four protocol-identity fields carrying that shortcut:

| Field | \|r\| with `Attack_label` |
|---|---|
| `dns.qry.name.len` | 0.966 |
| `mqtt.topic` | 0.881 |
| `mqtt.protoname` | 0.880 |
| `mqtt.conack.flags` | 0.877 |

Removing them collapses the linear model — and leaves Random Forest untouched:

| Model | Uncontrolled (48) | Leakage-controlled (44) | Δ |
|---|---|---|---|
| Random Forest | 1.0000 | **1.0000** | 0.0000 |
| Gradient Boosting | 1.0000 | 0.9999 | −0.0001 |
| MLP | 0.9999 | 0.9990 | −0.0009 |
| Logistic Regression | 1.0000 | **0.9566** | −0.0434 |

Random Forest classifies **all 30,449 held-out records without a single error** after leakage control.

The manuscript argues that the most parsimonious reading is *redundant shortcut encoding*: the screen removed the route to the shortcut that a hyperplane could exploit, and left others open for the non-linear learners. Correlation-based screening is therefore **necessary but not sufficient**, and a post-control accuracy of exactly 1.0000 should be read as a red flag, not a result.

The collapse is **not** an artefact of the encoding scheme. Under one-hot encoding, with encoder, scaler, and classifier all fitted on the training partition only:

| Encoding | Uncontrolled | Controlled |
|---|---|---|
| Label | 1.0000 | 0.9566 |
| One-hot | 1.0000 | 0.9566 |

Agreement to four decimal places. See `02_encoding_robustness.ipynb`.

---

## Data

This repository does **not** redistribute the dataset. Obtain `ML-EdgeIIoT-dataset.csv` (the ML-ready release) from its original publishers:

> M. A. Ferrag, O. Friha, D. Hamouda, L. Maglaras, and H. Janicke, "Edge-IIoTset: A new comprehensive realistic cyber security dataset of IoT and IIoT applications for centralized and federated learning," *IEEE Access*, vol. 10, pp. 40281–40306, 2022. doi: [10.1109/ACCESS.2022.3165809](https://doi.org/10.1109/ACCESS.2022.3165809)

Place the CSV alongside the notebooks, or upload it when prompted in Colab.

---

## Reproducing the results

### Google Colab (no installation)

1. Open [colab.research.google.com](https://colab.research.google.com) → **File → Upload notebook**.
2. Upload `01_main_analysis.ipynb`.
3. Run the cells in order. Upload the CSV when prompted.

### Locally

```bash
pip install -r requirements.txt
jupyter notebook 01_main_analysis.ipynb
```

Set `RANDOM_STATE = 42` (the default) to reproduce the published figures exactly.

---

## One reproducibility detail that matters

**Duplicates must be removed *after* dropping the thirteen identifier and payload fields, not before.**

While `frame.time`, `ip.src_host`, `tcp.payload` and the other identifier fields are present, almost every record is unique and only ~814 duplicates are detected. Once those fields are dropped, records that are identical in every *behavioural* feature collapse together, and **5,555** duplicates are found — yielding the 152,245 records reported in the manuscript.

Both notebooks enforce this ordering and assert the row count. If your run does not print `152,245`, stop: nothing downstream will be comparable.

---

## What this work does not claim

Stated plainly, because the manuscript's argument depends on the distinction:

- It does **not** claim to have *proven* residual leakage in the leakage-controlled feature set.
- It does **not** claim that prior published results using Edge-IIoTset are invalid.
- It does **not** claim that Random Forest's zero-error performance is definitely attributable to a shortcut rather than to genuine signal.

The decisive test is **grouped, session-aware cross-validation**. The publicly released ML-ready CSV omits capture-session identifiers, so it cannot be performed on that file. Reconstructing session provenance from the per-attack capture files that precede the merged release is identified in the manuscript as the highest-priority next step.

Contributions in that direction are welcome — open an issue.

---

## Citation

```bibtex
@article{dhaou2026leakage,
  author  = {Dhaou, Fatma Mohammed},
  title   = {Correlation-Based Leakage Screening Is Insufficient for
             {IoT/IIoT} Intrusion-Detection Benchmarks: Redundant
             Shortcut Encoding in {Edge-IIoTset}},
  journal = {IEEE Access},
  year    = {2026},
  note    = {Under review}
}
```

## License

MIT — see `LICENSE`. The Edge-IIoTset dataset is licensed separately by its original authors.
