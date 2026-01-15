# Capstone Project — Final Report
## Image Quality as a Hyperparameter  
**Author:** Rick O.

---

## 1. Problem Statement
Modern computer vision pipelines often assume model architecture is the primary performance lever. In practice, **input image quality** can be the binding constraint. This project evaluates whether **controlled image enhancement**—applied to training and inference imagery—can measurably change downstream performance **without retraining the model**.

---

## 2. Model Outcomes / Predictions
- **Task:** Supervised object detection (tree-point prediction)
- **Model:** Pretrained Green City TPP (VGG-style SFANet)
- **Output:** Predicted tree locations with associated detection metrics
- **Primary metric:** **Recall** (per original model author and prior work)
- **Secondary metrics:** Precision, F1, false positives, spatial distance

The model is intentionally **frozen** and treated as a **measurement instrument**. All observed changes are attributed to input-space manipulation.

---

## 3. Data Acquisition
- **Ground truth:** Street ROW Trees — City of Pasadena, CA  
- **Imagery:** NAIP aerial imagery (chip-based ingestion)
- **Experimental logs:** ~50 completed CVE runs with timestamped metrics

---

## 4. Data Preprocessing / Preparation
Image chips are enhanced using parameterized computer-vision operations:
- **Sharpening:** strength (`alpha`) and scale (`sigma`)
- **Contrast stretching:** lower/upper percentiles (`low_perc`, `high_perc`)
- **Brightness:** gamma correction (`gamma`)

Enhancements overwrite working directories temporarily; inference and evaluation are run; metrics are logged. Canonical metrics are joined to CVE parameters via validated temporal alignment.

---

## 5. Modeling
No new model training occurs in this phase. Instead:
- The frozen TPP model is run repeatedly under different **CVE schedules**
- Parameter sweeps function as **data-level hyperparameters**
- This isolates image quality effects from training variance

---

## 6. Model Evaluation
Evaluation is performed consistently across runs using:
- Recall (primary)
- Precision, F1
- False positives / negatives
- Mean distance to closest predicted tree

The analysis emphasizes **image-driven response surfaces** and **parameter interactions**, not model tuning.

---

## 7. Results

### Figure 1 — Recall vs Image Parameters
![Recall vs Image Parameters](results/final_figures/fig01_recall_vs_params.png)

*Recall varies nonlinearly across individual enhancement parameters. No single parameter dominates in isolation, motivating interaction analysis.*

---

### Figure 2 — Interaction Heatmaps (Median Recall)
![Interaction Heatmaps](results/final_figures/fig06_interaction_heatmaps_grid.png)

*Median recall surfaces reveal strong **interaction effects** among enhancement parameters. Brightness conditions sharpening behavior (gamma × alpha / sigma), while contrast window geometry (low × high percentiles) exhibits bounded regimes. These interactions demonstrate that visually similar enhancements can produce materially different model responses.*

---

### Figure 3 — Interaction Scatter Support
![Interaction Scatter Support](results/final_figures/fig07_interaction_scatter_support_grid.png)

*Raw run-level scatter confirms the heatmap structure and shows that interaction surfaces are supported by the underlying data rather than binning artifacts.*

---

### Figure 4 — Precision–Recall Across ~50 CVE Runs (Model Frozen)
![Precision–Recall Across Runs](results/final_figures/fig04_precision_vs_recall.png)

*Across the full experiment set, the frozen model responds coherently as image inputs change. Recall spans a meaningful range while precision degrades gradually, indicating that performance shifts are driven by input manipulation rather than unstable inference.*

---

### Figure 5 — “Pretty but Worse” Example (Qualitative)
![Pretty but Worse Example](results/final_figures/fig08_pretty_but_worse.png)

*A representative before/after enhancement pair shows improved visual clarity accompanied by a substantial recall drop. This illustrates a key finding: **aesthetic improvement does not guarantee model-aligned improvement**.*

---

## 8. Findings
- Image quality behaves like a **hyperparameter**, measurably shifting model behavior even when the model is frozen.
- **Interaction effects** dominate: brightness often conditions the impact of sharpening; contrast operates within bounded safe ranges.
- Visually “better” images can **harm recall**, reinforcing the need for model-aware image engineering.
- Recall gains do not arise from pathological behavior; tradeoffs remain coherent across runs.

---

## 9. Next Steps
- Formalize **recall-safe enhancement bands** for production use.
- Extend interaction analysis to task-specific objectives (e.g., localization error thresholds).
- Apply the same measurement framework to other CV tasks and datasets.

---

## 10. Repository Contents
- `results/final_figures/` — publication-ready figures used in this report
- Jupyter notebooks — analysis and figure generation
- Scripts — CVE application and metric aggregation

---

## Contact
Rick O.  
UC Berkeley Engineering — MIDS Capstone
