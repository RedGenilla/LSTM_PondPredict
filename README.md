# PondPredict: Transfer Learning-Based LSTM Forecasting of pH and Water Temperature for Philippine Bangus Pond Management

## Project Overview

PondPredict is a research project that investigates the use of Long Short-Term Memory (LSTM) networks and transfer learning to forecast water pH and temperature in Philippine bangus ponds.

The study addresses the limited availability of Philippine pond monitoring data by training an LSTM model on an Indonesian fishpond dataset and transferring the learned parameters to a model adapted using Philippine observations.

The project compares a Philippine-only LSTM baseline against a transfer learning-based LSTM using forecasting accuracy, statistical testing, chronological cross-validation, feature ablation, and different quantities of local training data.

The study also evaluates recursive multi-step forecasting to investigate predictions extending up to 24 hours ahead.

## Technologies Used

- Python 3.14.0
- Keras with PyTorch backend
- PyTorch
- NumPy and Pandas
- Scikit-learn
- SciPy
- Matplotlib
- Jupyter Notebook

Exact dependency versions are recorded in `requirements_final.txt`.

## Datasets

**Indonesian Source Dataset:** `data/pond_iot_2023.csv`

Contains fishpond water-quality measurements used to train the source-domain LSTM model. The study uses water pH and water temperature as the forecasting variables.

**Philippine Target Dataset:** `data/PH_data.csv`

Contains 288 hourly water pH and temperature observations used for Philippine-only training, transfer-learning adaptation, and evaluation.

The Philippine dataset is divided chronologically into:

| Partition | Observations |
|---|---:|
| Training | 201 |
| Validation | 43 |
| Testing | 44 |

Both forecasting models are evaluated using the same 38 one-hour-ahead test sequences.

## Model Architecture

The LSTM forecasting models use:

| Parameter | Configuration |
|---|---|
| Input features | Water pH and temperature |
| Input sequence | 6 consecutive hours |
| LSTM layers | 1 |
| LSTM units | 16 |
| Dropout | 0.20 |
| Dense output units | 2 |
| Forecasting targets | Next-hour pH and temperature |
| Batch size | 8 |
| Maximum epochs | 100 |
| Early stopping patience | 5 |

The source-domain LSTM is initially trained using Indonesian fishpond observations.

Transfer learning uses two training stages:

1. **Frozen stage:** The pretrained LSTM layer is frozen while a new Dense output layer is trained using Philippine observations.
2. **Fine-tuning stage:** The LSTM layer is unfrozen and further trained to adapt its learned parameters to the Philippine dataset.

The final selected fine-tuning learning rate is 0.0005.

## Project Notebooks

| Notebook | Description |
|---|---|
| `01_source_lstm.ipynb` | Indonesian source-model training and hyperparameter tuning |
| `02_philippine_baseline.ipynb` | Philippine-only baseline model training |
| `03_transfer_learning.ipynb` | Frozen-layer training and transfer-learning fine-tuning |
| `04_model_comparison.ipynb` | Held-out model comparison and statistical testing |
| `05_local_data_experiments.ipynb` | Experiments using 25%, 50%, 75%, and 100% local training data |
| `06_model_validation_and_feature_ablation.ipynb` | Chronological cross-validation and input-feature ablation |
| `07_recursive_multistep_forecasting.ipynb` | Recursive forecasting evaluation for 1–6 hours |
| `08_recursive_24h_evaluation.ipynb` | Recursive forecasting evaluation for 1–24 hours |

## Installation and Setup

**1. Clone the repository**

Clone this repository using Git, then open the PondPredict project folder in VS Code.

**2. Create a virtual environment**

On Windows PowerShell:

`py -3.14 -m venv venv`

**3. Activate the virtual environment**

`.\venv\Scripts\Activate.ps1`

**4. Install dependencies**

`python -m pip install -r requirements_final.txt`

**5. Open the notebooks**

Select the virtual environment as the Python kernel in VS Code or Jupyter.

Run the notebooks in numerical order if reproducing the complete experiment from training. Ensure the required datasets are available in the `data` folder.

For evaluation only, use the corresponding saved model checkpoints and scalers rather than retraining the models.

## Saved Models

The `models` folder contains the final trained model files and fitted scalers:

- `source_final.keras`: Indonesian source-domain LSTM
- `ph_baseline.keras`: Philippine-only baseline LSTM
- `transfer_final.keras`: Philippine-adapted transfer-learning LSTM
- `source_scaler.pkl`: Indonesian source-domain normalization
- `ph_scaler.pkl`: Philippine target-domain normalization

These files support reproducible evaluation and recursive inference without retraining.

## Final One-Hour Forecasting Results

The final models were evaluated using the same 38 held-out Philippine forecasting sequences.

| Target | Model | MAE | RMSE | R² |
|---|---|---:|---:|---:|
| pH | Philippine-only | 0.02277 | 0.02863 | 0.81068 |
| pH | Transfer learning | 0.02298 | 0.02805 | 0.81830 |
| Temperature | Philippine-only | 0.18708 | 0.23171 | 0.98144 |
| Temperature | Transfer learning | 0.15818 | 0.20454 | 0.98554 |

Transfer learning achieved lower temperature forecasting errors on the held-out test set. For pH forecasting, the results were mixed.

## Additional Experiments

**Statistical Comparison**

The models' paired forecasting errors were evaluated using the Shapiro-Wilk normality test, followed by either a paired-samples t-test or Wilcoxon signed-rank test.

**Chronological Cross-Validation**

Five chronological folds were used to assess forecasting performance across different training and evaluation periods.

**Feature Ablation**

Three input configurations were evaluated: both features, pH only, and temperature only. Each configuration retained two forecasting outputs.

**Local Training-Data Quantity**

The models were evaluated using 25%, 50%, 75%, and 100% of the available Philippine training partition.

**Recursive Multi-Step Forecasting**

The existing one-hour-trained LSTM models were reused to generate recursive forecasts for up to 24 hours without retraining.

Each predicted pH and temperature pair is fed back into the input window to generate the next hourly forecast. Actual future measurements are used only for evaluation.

The 1–6-hour experiment used 33 common forecast origins. The 1–24-hour experiment used 15 common forecast origins from the held-out Philippine test partition.

The extended-horizon results are exploratory. Prediction errors can accumulate over successive forecast steps, and the results do not establish dependable 12- or 24-hour operational accuracy.

## Reproducibility

The project retains the experimental notebooks, datasets, model checkpoints, fitted scalers, exported results, and generated figures.

The experiments use a configured random seed of 42 to support reproducibility. The package versions used in the development environment are documented in `requirements_final.txt`.

The original chronological test partition is kept separate from training and model selection.

The reported results are based on the available experimental runs and should not be interpreted as evidence of performance across multiple independent ponds, seasons, or random-seed repetitions.

## Research Limitations

The Philippine dataset contains only 288 hourly observations from a limited monitoring period. Model performance may therefore differ across other locations, seasons, or environmental conditions.

Recursive forecasts beyond one hour are exploratory, particularly at longer horizons, where accumulated forecasting errors can reduce accuracy.

The one-hour-ahead model remains the primary validated forecasting configuration used in the study.

## Project Scope

This repository documents the machine-learning development, training, evaluation, and recursive forecasting experiments for PondPredict.

The Flutter mobile application and its model-integration procedures are separate components of the overall PondPredict system.
