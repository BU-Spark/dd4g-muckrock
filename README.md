
# MuckRock Patient Safety — Dang Dinh (dddinh)

## Branch Overview
This branch contains data cleaning, merging, and exploratory data analysis 
for the FDA MAUDE complaint dataset and recall dataset.

## Notebooks

### 1. `foi_and_device_merge.ipynb`
- Loads and combines raw FOI complaint files (2015–2025)
- Concatenates multiple FOI_TEXT entries per MDR_REPORT_KEY
- Merges complaint dataset with FDA device catalog
- Reduces 46M raw entries to 19.2M unique complaint records
- Exports final merged dataset: `merged_foi_device.csv`

### 2. `complaint_eda.ipynb`
- EDA on merged complaint + device dataset
- Complaint volume trends by year and month
- Top complained devices, brands, and manufacturers
- Device category analysis and complaint heatmap
- ALARIS infusion pump 2022 spike investigation

### 3. `complaint_recalled_eda.ipynb`
- Joint analysis of complaint and recall datasets
- Complaint vs recall trend comparison (2015–2025)
- Correlation analysis between complaints and recalls
- Time lag analysis between first complaint and first recall
- Infusion pump FRN deep dive

## Outputs
`/graphs` — Contains all generated visualizations from EDA notebooks


## Requirements
```
pandas
matplotlib
seaborn
scikit-learn
jupyter
```