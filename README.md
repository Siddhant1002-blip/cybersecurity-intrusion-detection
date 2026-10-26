# cybersecurity-intrusion-detection

Project overview
This project explores and preprocesses the NSL-KDD network
intrusion-detection dataset. The current submission focuses on
exploratory data analysis (EDA), cleaning, feature engineering,
categorical encoding, numeric transformations, standardization, outlier
analysis, and class-imbalance handling.

Scope: This is a preprocessing-focused project report. Do not claim
model accuracy until a classifier has been trained and evaluated on a
held-out test set.
Dataset
- File: NSL_KDD_full_dataset.csv
- Observed shape in the supplied notebook: 147,907 rows × 42
  columns
- Target column: label
- Grouped target categories: Normal, DoS, Probe, R2L, and
  U2R
- Binary target: is_attack
- Initial notebook inspection reported zero missing values and zero
  duplicate rows.
Check the dataset's source and redistribution terms before uploading the
CSV publicly. If needed, include download instructions instead of the
data file.
Suggested repository structure
cybersecurity-intrusion-detection/
├── README.md
├── Project_Report.pdf
├── NSL_KDD_Preprocessing_Single_Dataset.ipynb
├── NSL_KDD_full_dataset.csv
└── requirements.txt
Methods
1. Inspect shape, data types, missing values and duplicate records.
2. Drop duplicates if present and remove the constant
   num_outbound_cmds feature.
3. Group raw attack labels into five categories and create a binary
   attack indicator.
4. Explore class distributions, numerical feature distributions,
   categorical frequencies and feature correlations.
5. Engineer total-byte, byte-rate, connection-rate, byte-ratio and
   error-rate features.
6. One-hot encode protocol_type, service and flag.
7. Apply log1p to selected skewed non-negative features.
8. Standardize selected numeric features using StandardScaler.
9. Document outlier detection and removal only after the final code has
   been run and verified.
10. Apply SMOTE to training data only; leave test data unchanged.
Requirements
Python 3.10+ is recommended. Install the dependencies:
pip install -r requirements.txt
How to run
1. Clone or download the repository.
2. Place NSL_KDD_full_dataset.csv beside the notebook, if distributed
   separately.
3. Open the notebook in Jupyter or Google Colab.
4. Run all cells from top to bottom.
5. Verify plots, output shapes, outlier counts and class counts before
   submission.
Reproducibility and cautions
- Fit encoders and scalers on training data only; transform test data
  with the fitted objects.
- Split data before fitting preprocessing steps. Avoid using the test
  set to learn categories or scaling parameters.
- Apply SMOTE only after the split and only to the training data.
- Network outliers may be genuine attacks. Do not remove unusual
  records without explaining the method and justification.
- Avoid committing large generated processed CSVs unless required.
Current status
The notebook should be rerun after edits so saved outputs and plots
match the submitted code. Add actual outlier counts and model-evaluation
results only after running the final workflow.
