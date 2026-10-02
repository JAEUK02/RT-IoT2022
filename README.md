# RT-IoT2022: IoT Traffic Classification

**An offline notebook study of multiclass IoT network traffic.** It uses a Random Forest baseline to inspect per-class detection performance, class imbalance, and influential numeric features.

**Start here:** [`RT-IoT2022-2.ipynb`](RT-IoT2022-2.ipynb), which contains the data-loading, model, evaluation, and visualization cells in one place.

## Problem and contribution record

Aggregate accuracy can hide poor coverage of rare traffic classes. This study reads the class distribution and compares class-level precision, recall, and F1 with feature-importance and PCA visualizations.

The public notebook is recorded in [JAEUK02's upload commit](https://github.com/JAEUK02/RT-IoT2022/commit/eac6a820853c0f76c53e734c5eb3e7ffcaa4e527). Its scope is exploratory data analysis and an offline classification baseline.

## Implementation

1. Load RT-IoT2022 through `ucimlrepo.fetch_ucirepo(id=942)`.
2. Inspect traffic labels and replace the service placeholder `-` with `unknown`.
3. Select 81 numeric features from the dataset's 83 feature columns.
4. Use an 80/20 random split with `random_state=42`; train `RandomForestClassifier(random_state=42)`.
5. Produce a held-out classification report and confusion matrix, feature-importance charts, a recall chart, and a two-component PCA plot.

**Stack:** Python, ucimlrepo, pandas, scikit-learn, matplotlib, and seaborn.

## Saved evaluation outputs

The notebook's stored output reports 123,117 rows, a 98,493-row training set, and a 24,624-row test set. In the held-out report:

| Measure | Saved value |
| --- | ---: |
| Macro precision | 0.97 |
| Macro recall | 0.95 |
| Macro F1 | 0.96 |
| `NMAP_FIN_SCAN` recall | 0.67, with 3 test rows |
| `Metasploit_Brute_Force_SSH` recall | 0.83, with 6 test rows |

Values are rounded as displayed by `classification_report`. The small support for some classes matters when interpreting performance; an aggregate score alone is insufficient.

## Local exploration

In a fresh Python environment, install the notebook's libraries and open it:

```bash
git clone https://github.com/JAEUK02/RT-IoT2022.git
cd RT-IoT2022
python3 -m venv .venv
source .venv/bin/activate
python -m pip install ucimlrepo pandas scikit-learn matplotlib seaborn jupyter
jupyter notebook RT-IoT2022-2.ipynb
```

Run cells in order. Dataset retrieval requires internet access. Dependency versions are not locked, and the saved outputs were inspected rather than rerun during this documentation review.

## Status and limits

- The main split is random and unstratified. Rare classes have very small test samples.
- The later recall plot predicts over the full dataset, including training rows; use the earlier held-out classification report to discuss test performance.
- Feature importance is model-specific, and the PCA visualization is exploratory.
- The notebook contains one Random Forest baseline. It does not benchmark LightGBM, device inference latency, production deployment, or cross-dataset generalization.
