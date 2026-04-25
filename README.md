# Earth Observation Land Cover Classification

This repository contains an end-to-end Earth Observation pipeline for land-cover classification over the Delhi-NCR region using satellite imagery, geospatial processing, and deep learning.

The pipeline covers:

- Q1: Spatial reasoning and image filtering
- Q2: Label construction from ESA WorldCover raster
- Q3: CNN training and evaluation

All major steps are implemented in a single notebook:

- `earth_observation.ipynb`

Additional documentation:

- `USER_GUIDE.md`

---

## Project Structure

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
```

---

## Problem Statement

Given Delhi-NCR geospatial boundaries, Sentinel-2 RGB patches, and ESA WorldCover raster:

1. Create a 60 km x 60 km uniform grid over Delhi-NCR (EPSG:32644).
2. Filter satellite images whose center coordinates fall inside the grid.
3. Build labels by extracting 128 x 128 raster patches centered on each image point and assigning dominant class (mode).
4. Train a CNN (ResNet18) for land-cover classification.
5. Evaluate with custom F1, `torchmetrics` F1, confusion matrix, and prediction visualization.

---

## Setup

### 1) Create and activate environment

Windows (PowerShell):

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
```

### 2) Install dependencies

```powershell
pip install -r requirements.txt
```

### 3) Launch Jupyter

```powershell
jupyter notebook
```

Open and run:

- `earth_observation.ipynb`

Run all cells from top to bottom.

---

## Quick Run Order

1. Open `earth_observation.ipynb`.
2. Run imports and path setup cells.
3. Run Q1 cells (grid + filtering).
4. Run Q2 cells (label extraction + split + class distribution).
5. Run Q3 cells (training + evaluation + confusion matrix).
6. Run extra metrics cell (Accuracy/Precision/Recall/F1/RMSE/MAE).

---

## Data Assumptions

- RGB image filenames follow: `latitude_longitude.png` (example: `28.2056_76.8558.png`)
- Coordinate system for image coordinates: `EPSG:4326`
- Grid generation CRS: `EPSG:32644`
- Raster labels source: `worldcover_bbox_delhi_ncr_2021.tif`

---

## Notebook Outputs

After running notebook, these key files are generated:

### Processed data

- `outputs/processed/q1_all_images_points.csv`
- `outputs/processed/q1_filtered_images_points.csv`
- `outputs/processed/q2_labeled_dataset.csv`

### Figures

- `outputs/figures/q1_grid_static.png`
- `outputs/figures/q1_satellite_overlay.html` (optional; if basemap cell runs)
- `outputs/figures/q2_class_distribution.png`
- `outputs/figures/q3_confusion_matrix.png`
- `outputs/figures/q3_correct_incorrect_examples.png`

### Model

- `outputs/models/q3_resnet18_best.pt`

---

## Evaluation

The notebook reports:

- Accuracy
- Precision
- Recall
- Custom macro-F1 implementation
- `torchmetrics` macro-F1 (`MulticlassF1Score`)
- `sklearn` macro-F1 (reference)
- Weighted F1
- RMSE and MAE (class-index based, only if explicitly requested)
- Confusion matrix and per-class classification report

---

## Edge Case Handling

Implemented decisions:

- Patch extraction is boundless with fill-value for out-of-raster area
- High no-data patches are skipped (`nodata_ratio > 0.6`)
- Unknown class codes (outside ESA 11-class set) are skipped
- Random split uses fixed seed for reproducibility

---

## Reproducibility

Fixed random seed is set for:

- Python random
- NumPy
- PyTorch (CPU and CUDA, if available)

## GitHub Push Commands

Use these commands from project root:

```powershell
git init
git add .
git commit -m "Add complete Earth Observation pipeline notebook, docs, and report template"
git branch -M main
git remote add origin <your-repo-url>
git push -u origin main
```

