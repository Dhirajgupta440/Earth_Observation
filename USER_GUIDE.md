# User Guide - Earth Observation Project

This guide explains how to run, troubleshoot, and submit the Earth Observation notebook project.

## 1) Prerequisites

- Python 3.9+ (recommended: 3.10/3.11)
- Windows PowerShell
- At least 8 GB RAM
- Jupyter Notebook installed (`pip install notebook`)

## 2) Expected Folder Layout

```text
Earth_Observation/
|-- Data/
|   |-- rgb/
|   |-- delhi_ncr_region.geojson
|   |-- delhi_airshed.geojson
|   |-- worldcover_bbox_delhi_ncr_2021.tif
|
|-- outputs/
|   |-- figures/
|   |-- processed/
|   |-- models/
|
|-- earth_observation.ipynb
|-- requirements.txt
|-- README.md
|-- report.md
|-- USER_GUIDE.md
```

## 3) Environment Setup

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
pip install -r requirements.txt
jupyter notebook
```

Open `earth_observation.ipynb` and run all cells in order.

## 4) Notebook Pipeline Overview

### Q1 - Spatial Reasoning and Filtering

- Loads Delhi-NCR boundary.
- Reprojects to `EPSG:32644`.
- Builds uniform `60 km x 60 km` grid.
- Computes cell corners and centers.
- Parses image coordinates from filename (`lat_lon.png`).
- Filters images whose centers fall inside the grid.
- Saves CSV outputs and static map.

### Q2 - Label Construction

- For each filtered image, extracts `128 x 128` patch from WorldCover raster.
- Assigns label by mode (dominant class).
- Maps ESA class code to standard land-cover labels.
- Handles no-data and unknown class edge cases.
- Splits into `60/40` train-test set.
- Saves labeled dataset and class distribution plot.

### Q3 - Model Training and Evaluation

- Builds ResNet18 classifier.
- Trains model on training split.
- Evaluates on test split.
- Computes:
  - Accuracy
  - Precision
  - Recall
  - Macro/Weighted F1
  - Confusion matrix
  - RMSE/MAE on class indices (optional/reporting-only)
- Saves best model and evaluation figures.

## 5) Generated Outputs

### Processed CSVs

- `outputs/processed/q1_all_images_points.csv`
- `outputs/processed/q1_filtered_images_points.csv`
- `outputs/processed/q2_labeled_dataset.csv`

### Figures

- `outputs/figures/q1_grid_static.png`
- `outputs/figures/q1_satellite_overlay.html` (optional)
- `outputs/figures/q2_class_distribution.png`
- `outputs/figures/q3_confusion_matrix.png`
- `outputs/figures/q3_correct_incorrect_examples.png`

### Model

- `outputs/models/q3_resnet18_best.pt`

## 6) Common Errors and Fixes

### Error: Missing module

Fix:

```powershell
pip install -r requirements.txt
```

### Error: File not found in Data folder

Fix:

- Ensure exact filenames exist in `Data/`.
- Ensure notebook is run from project root.

### Error: `y_true` not defined in extra metrics cell

Fix:

- Run evaluation cells before running the extra metrics cell.

### Error: Basemap not loading

Fix:

- This is optional. Continue with static plot outputs.

## 7) What to Put in Final Report

- Before/after filtering image counts.
- Class distribution and imbalance note.
- Accuracy, Precision, Recall, F1 (macro/weighted).
- Confusion matrix insights (most confused class pairs).
- Optional RMSE/MAE values with note that they are not standard for classification.

## 8) Final Submission Steps

1. Run notebook end-to-end.
2. Verify outputs are generated.
3. Fill `report.md` metric placeholders.
4. Commit project to GitHub repo.
5. Submit repository link in Google Form.

