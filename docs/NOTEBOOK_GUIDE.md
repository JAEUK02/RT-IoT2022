# Notebook guide

Use this map to inspect [`RT-IoT2022-2.ipynb`](../RT-IoT2022-2.ipynb), understand its shared state, and distinguish test evaluation from later exploratory plots. The [README](../README.md) summarizes the study and archived metrics.

**Cell indices are zero-based positions in the notebook JSON, including the final empty cell.** They are not the saved Jupyter execution counts. This guide refers to the [reviewed snapshot](https://github.com/JAEUK02/RT-IoT2022/blob/a21ee10cc6db6a0e269751be48a49c74c0486f30/RT-IoT2022-2.ipynb).

## Reading and dependency map

| Cell index | Saved execution count | Role and shared state |
| --- | --- | --- |
| 0 | 8 | Retrieve UCI dataset 942; define `X` and target DataFrame `y`; print shape and class distribution |
| 1 | 12 | Inspect features, replace the service placeholder, define `X_numeric`, and import pandas |
| 2 | 18 | Create the 80/20 train/test split from `X_numeric` and `y` |
| 3 | 22 | Flatten split labels, fit `model`, predict `X_test`, and print the held-out confusion matrix/report |
| 4 | 24 | Plot top-10 feature importances from `model` |
| 5 | 34 | Replace `y_pred` with predictions for all `X_numeric`; plot full-dataset class recall |
| 6 | 36 | Plot top-20 feature importances from the same fitted model |
| 7 | 40 | Fit two-component PCA on all numeric rows and plot labels |
| 8 | null | Empty cell |

For a quick read, inspect cells 0–2 for the data and split, cell 3 for held-out evaluation, then cells 4 and 6 for feature importance. Cell 5 is a different evaluation population; it must not be substituted for cell 3's test report. The notebook contains one fitted Random Forest, not a new model for each chart.

## What enters the model

Cell 1 selects only columns matching `int64` or `float64` through `select_dtypes`. The saved run has 83 input feature columns and 81 selected numeric columns. Its nonnumeric `proto` and `service` columns are not encoded into the classifier.

In particular, changing `service` from `-` to `unknown` is an inspection/cleanup step in `X`; that string column is subsequently excluded from `X_numeric`. It should not be described as categorical-feature encoding or as a learned service feature.

Cell 2 passes `X_numeric` directly to the split. The notebook does not add feature scaling, imputation, class balancing, or stratification. The target remains the multiclass `Attack_type` labels returned by the dataset, rather than a binary normal/attack conversion.

## Running and rerunning cells

The README lists the imported packages and a local notebook setup. Dataset retrieval in cell 0 needs network access. There is no bundled CSV snapshot, data checksum, trained model, or dependency lock in this repository. The notebook metadata records Python 3.12.4, but this is historical metadata, not a tested minimum or a complete environment specification.

For a new exploration, start with a fresh kernel and follow the dependency order from cell 0. Two state changes matter when rerunning a subset:

- **Cell 3 is not independently repeatable.** It assigns `y_train = y_train.values.ravel()` and the same for `y_test`. After one successful execution, those variables are NumPy arrays and no longer expose `.values`. Re-executing cell 3 alone therefore fails at that conversion. Re-execute cell 2 first to recreate the split-label DataFrames, then cell 3. This follows from inspecting the assignments; training was not rerun during this review.
- **Cell 5 overwrites `y_pred`.** It then has one prediction per full-dataset row, whereas `y_test` refers only to the held-out split. Do not pair that overwritten variable with `y_test`. To return to the notebook's held-out evaluation state, recreate the split and run cell 3 as above.

The feature-importance cells require `model` from cell 3 and column names from cell 1. The PCA cell requires `X_numeric`, the original full target `y`, and pandas state; it does not consume the classifier or its predictions.

## Reading the stored evaluation

Cell 3 is the source of the README's test precision, recall, F1, and class support. Its printed confusion matrix is unlabeled; its 12 row/column positions follow the same class order printed in the report:

1. `ARP_poisioning`
2. `DDOS_Slowloris`
3. `DOS_SYN_Hping`
4. `MQTT_Publish`
5. `Metasploit_Brute_Force_SSH`
6. `NMAP_FIN_SCAN`
7. `NMAP_OS_DETECTION`
8. `NMAP_TCP_scan`
9. `NMAP_UDP_SCAN`
10. `NMAP_XMAS_TREE_SCAN`
11. `Thing_Speak`
12. `Wipro_bulb`

Rows are true labels and columns predicted labels. Preserve the dataset's spelling and capitalization when matching labels. The rounded `1.00` accuracy does not mean zero mistakes: the saved matrix includes off-diagonal counts. Likewise, interpret the `NMAP_FIN_SCAN` recall alongside its support of only three test rows.

## Scope of the plots

- Cells 4 and 6 display impurity-based feature importance from the same forest fitted in cell 3. They do not report permutation importance, feature-selection experiments, or causal effects.
- Cell 5 predicts over the full dataset, including training rows. Its `attack_labels` variable actually includes every class row in the report, including `MQTT_Publish`, `Thing_Speak`, and `Wipro_bulb`; it does not filter the report to attack classes.
- Cell 7 fits PCA directly on the complete, unscaled numeric feature matrix. Its axes are exploratory projections affected by feature scale. PCA is not a preprocessing stage used to train the Random Forest, and the plot does not evaluate held-out classification or establish unseen-attack detection.

## Review record

The notebook's committed source, metadata, and stored text outputs were inspected, and its code cells passed syntax parsing without execution. No dataset retrieval, training, or new evaluation was performed. A future reproducibility record should include the exact fetched data/schema, dependency versions, hardware, source revision, and newly generated outputs.
