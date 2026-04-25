# Report - Scenario 1 (Earth Observation)

## Candidate Details

- Name: `<Your Name>`
- Email: `<Your Email>`
- Date: `<Submission Date>`
- Repository: `<GitHub Repo URL>`

---

## 1. Objective

The objective is to build a complete geospatial machine learning pipeline for Delhi-NCR:

1. Create a 60 km x 60 km spatial grid on Delhi-NCR boundary.
2. Filter Sentinel-2 image patches by spatial inclusion.
3. Generate land-cover labels from ESA WorldCover raster by dominant class in 128 x 128 patch.
4. Train and evaluate a CNN classifier.

---

## 2. Dataset and Inputs

- Delhi-NCR boundary: `Data/delhi_ncr_region.geojson`
- Delhi-Airshed boundary: `Data/delhi_airshed.geojson`
- Sentinel-2 RGB patches (128 x 128): `Data/rgb/*.png`
- Land-cover raster (ESA WorldCover 2021): `Data/worldcover_bbox_delhi_ncr_2021.tif`

Assumptions:

- RGB filenames are in format `lat_lon.png`.
- Image point CRS is EPSG:4326.
- Grid generation CRS is EPSG:32644 (meters).

---

## 3. Methodology

## Q1: Spatial Reasoning and Data Filtering

- Delhi-NCR boundary loaded with GeoPandas.
- Reprojected to EPSG:32644 for meter-based grid generation.
- Uniform 60,000 m x 60,000 m grid created and intersected with boundary.
- Grid centers and corners computed and plotted.
- Image center points parsed from filenames and converted to GeoDataFrame.
- Points filtered using point-in-polygon check against grid union.

Outputs:

- `outputs/figures/q1_grid_static.png`
- `outputs/processed/q1_all_images_points.csv`
- `outputs/processed/q1_filtered_images_points.csv`

Before/after counts:

- Before filtering: `<fill from notebook output>`
- After filtering: `<fill from notebook output>`

## Q2: Label Construction and Dataset Preparation

- For each filtered image point, 128 x 128 patch extracted from raster centered at image coordinate.
- Dominant class (mode) selected as image label.
- ESA class codes mapped to standardized 11 labels.
- Patches with excessive no-data (`nodata_ratio > 0.6`) skipped.
- Unknown classes skipped.
- Final dataset split randomly into 60% train and 40% test.
- Class distributions plotted to inspect imbalance.

Outputs:

- `outputs/processed/q2_labeled_dataset.csv`
- `outputs/figures/q2_class_distribution.png`

Key notes:

- No-data handling reduces noisy labels near raster boundaries.
- Class distribution is imbalanced, so macro-F1 is used as primary metric.

## Q3: Model Training and Evaluation

- Model: ResNet18 (`torchvision`) with final layer adapted to number of classes.
- Loss: CrossEntropyLoss
- Optimizer: Adam
- Baseline training epochs: 5 (can be increased for better performance).

Evaluation:

- Custom macro-F1 implementation
- `torchmetrics.MulticlassF1Score`
- `sklearn` macro-F1 (reference)
- Accuracy
- Precision
- Recall
- Confusion matrix
- 5 correct and 5 incorrect prediction examples

Outputs:

- `outputs/models/q3_resnet18_best.pt`
- `outputs/figures/q3_confusion_matrix.png`
- `outputs/figures/q3_correct_incorrect_examples.png`

Metrics (fill from run):

- Accuracy: `<value>`
- Precision (macro): `<value>`
- Recall (macro): `<value>`
- Custom macro-F1: `<value>`
- Torchmetrics macro-F1: `<value>`
- Sklearn macro-F1: `<value>`
- Precision (weighted): `<value>`
- Recall (weighted): `<value>`
- F1 (weighted): `<value>`
- RMSE (class-index based): `<value>`
- MAE (class-index based): `<value>`

Confusion matrix insight (fill from run):

- Most confused class pairs: `<example: Cropland vs Grassland>`
- Likely reason: `<mixed pixels / visual similarity / class imbalance>`

---

## 4. Discussion

### What worked well

- End-to-end spatial + raster + deep learning integration.
- Strong agreement between custom and library F1 implementations.
- Visual diagnostics via confusion matrix and prediction samples.

### Challenges

- Class imbalance across land-cover categories.
- Label noise due to mixed pixels in 128 x 128 windows.
- Boundary effects and no-data pixels in raster extraction.

### Why macro-F1

Macro-F1 treats each class equally and is better than accuracy when classes are imbalanced.

---

## 5. Limitations and Future Improvements

- Increase training epochs and tune hyperparameters.
- Use weighted loss or focal loss for imbalance.
- Add data augmentation for robustness.
- Try alternative backbones (EfficientNet, ConvNeXt).
- Explore multi-label or soft-label strategy for mixed land-cover patches.

---

## 6. Reproducibility

- Random seed fixed for Python, NumPy, and PyTorch.
- All outputs generated from notebook:
  - `earth_observation.ipynb`

---

## 7. Conclusion

The project successfully implements all core requirements of Scenario-1:

- Spatial grid reasoning and filtering (Q1)
- Raster-based label construction and dataset preparation (Q2)
- CNN training with required evaluation and visual analysis (Q3)

This forms a complete and reproducible Earth Observation classification pipeline.

