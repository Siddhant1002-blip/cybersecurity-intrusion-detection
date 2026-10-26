# Cybersecurity Intrusion Detection — NSL-KDD Data Preprocessing

## 1. Project overview

This project prepares the NSL-KDD network-traffic dataset for a cybersecurity intrusion-detection machine-learning workflow. It explores the distribution of normal and attack traffic, engineers additional features, encodes categorical variables, scales numerical features, detects potential outliers in normal training traffic, and applies SMOTE to reduce training-class imbalance.

**Project stage:** Data exploration and preprocessing. This report does not claim that a predictive model has been trained or evaluated.

## 2. Dataset

The supplied CSV contains **147,907 rows and 42 columns** before preprocessing. The target column `label` contains 40 distinct labels: `normal` and 39 attack types.

The project groups labels into five broader categories:

- **Normal** — normal network traffic
- **DoS** — denial-of-service attacks
- **Probe** — scanning and probing activity
- **R2L** — remote-to-local attacks
- **U2R** — user-to-root attacks

The dataset inspection in the notebook reported **0 missing values** and **0 duplicate rows**. The preprocessing step drops the constant/unused `num_outbound_cmds` column.

### Attack-category counts before preprocessing

| Category | Records |
|---|---:|
| Normal | 76,967 |
| DoS | 52,985 |
| Probe | 13,954 |
| R2L | 3,882 |
| U2R | 119 |
| **Total** | **147,907** |

These values describe the uploaded dataset and the category mapping used in the notebook.

## 3. Exploratory data analysis (EDA)

The notebook includes the following visualizations:

1. Bar chart comparing normal traffic with attack traffic.
2. Bar chart showing the five attack categories.
3. Histograms for `duration`, `src_bytes`, `dst_bytes`, `count`, and `srv_count`.
4. Bar charts for the most frequent values of `protocol_type`, `service`, and `flag`.
5. Correlation heatmap for numeric input features.

The histograms help show skewed numeric distributions. The categorical charts show the most common protocol, service, and connection flags. The correlation heatmap is exploratory and is not itself a feature-selection decision.

## 4. Feature engineering and preprocessing

### Feature engineering

The notebook creates these additional features:

- `total_bytes`
- `bytes_per_second`
- `src_bytes_per_second`
- `dst_bytes_per_second`
- `src_dst_byte_ratio`
- `connections_per_second`
- `failed_login_rate`
- `error_rate_total`
- `host_error_rate_total`

Zero durations are replaced with 1 **for the denominator used in rate calculations**, preventing division by zero.

### Categorical encoding

One-hot encoding is applied to `protocol_type`, `service`, and `flag`. The encoded feature matrix contains **130 input features** in the current notebook run.

### Train/test split

The data is split into training and test sets using an 80:20 ratio and `random_state=42`. Stratification uses `attack_category`.

| Split | Rows before outlier removal |
|---|---:|
| Training | 118,325 |
| Test | 29,582 |
| **Total** | **147,907** |

### Log transformation and scaling

`log1p` is applied to selected skewed numeric features. `StandardScaler` is fitted on the training set and then applied to the test set, avoiding fitting the scaler on test data. Binary one-hot columns are left unscaled.

### Outlier detection

`IsolationForest` with `contamination=0.01` is fitted to **normal-class training rows only**. Detected outliers are removed from the training set only; the test set remains unchanged. Outlier detection is a heuristic, so removed records should be interpreted as potential outliers, not proven errors.

In the notebook's recorded run, **616** training rows were removed, leaving **117,709** training rows. This left the following training category counts:

| Category | Rows after outlier removal |
|---|---:|
| Normal | 60,957 |
| DoS | 42,388 |
| Probe | 11,163 |
| R2L | 3,106 |
| U2R | 95 |
| **Total** | **117,709** |

### Class balancing with SMOTE

SMOTE is applied to the training data only. The notebook sets the target size for each minority category below the threshold to 10% of the largest training category. The test set is not resampled.

After moving outlier detection before SMOTE, re-run the notebook from the first cell so that the saved SMOTE dataset is regenerated from the post-outlier training data. With the recorded category counts above and the current `int(max_class_count * 0.10)` rule, the expected resampled counts are:

| Category | Expected training rows after SMOTE |
|---|---:|
| Normal | 60,957 |
| DoS | 42,388 |
| Probe | 11,163 |
| R2L | 6,095 |
| U2R | 6,095 |
| **Total** | **126,698** |

These are expected counts based on the current code and recorded outlier results; replace them if your final clean run reports different values.

## 5. Output files

After running all notebook cells successfully, the notebook writes:

- `train_processed.csv` — scaled training features with `attack_category` and `is_attack`.
- `test_processed.csv` — scaled test features with `attack_category` and `is_attack`; no SMOTE is applied.
- `train_processed_smote.csv` — SMOTE-resampled training features with `attack_category` and `is_attack`.

The notebook's previously saved `train_processed_smote.csv` was generated **before** the outlier cell affected the training set, so it is stale relative to the corrected order. Re-run all cells and replace the CSV outputs before submitting.

## 6. Important reproducibility notes

- Keep the dataset CSV in the same folder as the notebook. The notebook accepts `NSL_KDD_full_dataset.csv` and falls back to `NSL_KDD_full_dataset(1).csv`.
- Run the notebook from top to bottom after restarting the kernel.
- Verify that all cells complete without errors and that the printed output shapes and category counts match the latest run.
- Commit the notebook, this report, and `requirements.txt` to the public GitHub repository if required by your instructor.
- Avoid committing large generated CSV files unless the project instructions require them or GitHub file-size limits are satisfied.
- This report describes preprocessing only. Add model training, suitable metrics (such as precision, recall, F1-score, and a confusion matrix), and results only if those steps are actually implemented and run.

## 7. Conclusion

The workflow provides an initial preprocessing pipeline for intrusion-detection data, including EDA, feature engineering, categorical encoding, train/test splitting, scaling, outlier detection on normal training traffic, and training-only SMOTE. The corrected cell order is important: outlier detection must occur before SMOTE and before the processed files are saved, so that the output files consistently reflect the final pipeline.
