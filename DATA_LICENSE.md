# Data and Model Artifact Notice

## Original dataset

This repository uses the public **ApisTox** dataset:

- Adamczyk J, Poziemski J, Siedlecki P. *ApisTox: a new benchmark dataset for the classification of small molecules toxicity on honey bees*. Scientific Data, 2025.
- DOI: <https://doi.org/10.1038/s41597-024-04232-w>
- Official repository: <https://github.com/j-adamczyk/ApisTox_dataset>

The official ApisTox repository identifies the dataset license as **Creative Commons Attribution-NonCommercial 4.0 International (CC BY-NC 4.0)**:

<https://creativecommons.org/licenses/by-nc/4.0/>

Users must follow the original dataset license, provide appropriate attribution, and must not use the dataset for commercial purposes without obtaining any permission required by the original rights holders.

## Derived files in this repository

The following directories contain data or artifacts derived from ApisTox:

- `processed_data/`
- `saved_models/`
- data-dependent outputs contained in `notebooks/`
- data-dependent tables and figures contained in `reports/`

These materials are provided only for non-commercial education, reproducibility, and portfolio demonstration. Their inclusion in this repository does not change or replace the license of the original ApisTox dataset.

## Scientific-use limitation

The saved model produces statistical prediction scores. It is not an experimental toxicity assay, does not establish biological causality, and must not be used as a substitute for toxicological testing, regulatory assessment, or safety decisions.

## Software notice

Third-party Python packages, including RDKit, scikit-learn, pandas, NumPy, Matplotlib, IPython, Jupyter Notebook, and joblib, retain their own licenses. This notice does not modify those licenses.
