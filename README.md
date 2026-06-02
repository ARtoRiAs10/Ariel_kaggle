# Ariel_kaggle – A Curated Toolkit for Kaggle Competition Automation 🚀

---

## Badges
| | |
|---|---|
| **Python** | `>=3.9` |
| **License** | ![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg) |
| **PyPI** | ![PyPI - Version](https://img.shields.io/pypi/v/ariel-kaggle.svg) |
| **CI** | _(no CI badges detected)_ |

---

## Short description
**Ariel_kaggle** is a lightweight, opinionated Python library that streamlines the end‑to‑end workflow of Kaggle competitions. It bundles utilities for data ingestion, feature engineering, model training, and submission generation into a single, well‑documented package. Designed for data scientists, machine‑learning engineers, and Kaggle enthusiasts, the toolkit removes repetitive boilerplate so you can focus on model creativity and experimentation. Whether you are entering your first competition or automating a production‑grade pipeline, Ariel_kaggle provides a clear, reproducible structure that works out‑of‑the‑box.

---

## ✨ Features
- **🔄 Seamless Kaggle API integration** – Authenticate, download datasets, and submit predictions with a single function call.
- **📂 Project scaffolding** – `ariel init` creates a fully‑featured directory layout (data, notebooks, src, submissions) adhering to best practices.
- **🛠️ Reusable preprocessing pipelines** – Built‑in transformers for common tasks (categorical encoding, missing‑value imputation, scaling) that can be chained with `sklearn.pipeline`.
- **⚡ Model‑agnostic training helper** – One‑liner training loops that support any scikit‑learn, XGBoost, LightGBM, or PyTorch model.
- **📊 Automatic EDA reports** – Generate interactive pandas‑profiling reports with `ariel eda`.
- **🚢 Submission manager** – Versioned CSV submissions, automatic naming, and optional leaderboard upload.
- **🧪 Integrated test harness** – Unit tests for data integrity, model reproducibility, and submission format validation.
- **📝 Comprehensive logging** – Structured JSON logs for every pipeline step, making debugging and experiment tracking trivial.

---

## 📋 Table of Contents
- [🏗️ Architecture / How it works](#-architecture--how-it-works)
- [⚙️ Prerequisites](#-prerequisites)
- [🚀 Installation](#-installation)
- [📖 Quick Start](#-quick-start)
- [🔧 Configuration](#-configuration)
- [📚 API Reference / Usage](#-api-reference--usage)
- [🗂️ Project Structure](#-project-structure)
- [🧪 Running Tests](#-running-tests)
- [🤝 Contributing](#-contributing)
- [📄 License](#-license)

---

## 🏗️ Architecture / How it works
Ariel_kaggle follows a modular, layered architecture:

1. **CLI Layer** – `ariel` command parses sub‑commands (`init`, `download`, `train`, `submit`, `eda`) using **Typer**.
2. **Service Layer** – Core services (`KaggleService`, `PipelineService`, `SubmissionService`) encapsulate external interactions and business logic.
3. **Domain Layer** – Reusable objects such as `Dataset`, `FeaturePipeline`, and `ModelWrapper` that model the competition artefacts.
4. **Infrastructure Layer** – Thin adapters for the Kaggle API, file‑system I/O, and logging (JSON‑log to `logs/`).

Each layer communicates through well‑defined Python protocols, enabling easy swapping of components (e.g., replace `KaggleService` with a mock for offline development).

---

## ⚙️ Prerequisites
| Requirement | Minimum version | Notes |
|-------------|----------------|-------|
| Python | 3.9 | Tested on 3.9‑3.12 |
| Operating System | Linux/macOS/Windows | No OS‑specific dependencies |
| Kaggle CLI | 1.5.12 | Required for authentication (`kaggle competitions download …`) |
| pip | 21.0 | For package installation |
| Optional: Git | 2.20 | For version‑controlled submissions |

---

## 🚀 Installation
```bash
# 1️⃣ Create a clean virtual environment
python -m venv .venv
source .venv/bin/activate   # Windows: .venv\Scripts\activate

# 2️⃣ Upgrade pip and install the package from PyPI
pip install --upgrade pip
pip install ariel-kaggle

# 3️⃣ (Optional) Install extra dependencies for notebooks and profiling
pip install "ariel-kaggle[notebooks,eda]"
```

If you prefer to work directly from the repository:

```bash
git clone https://github.com/ARtoRiAs10/Ariel_kaggle.git
cd Ariel_kaggle
python -m venv .venv
source .venv/bin/activate
pip install -e .
```

---

## 📖 Quick Start
```bash
# Initialise a new competition workspace
ariel init house-prices-advanced-regression-techniques

# Authenticate with Kaggle (once)
kaggle competitions submit -c house-prices-advanced-regression-techniques -f dummy.csv -m "test"

# Download the dataset into the workspace
ariel download

# Generate an exploratory data analysis report
ariel eda --output reports/eda.html

# Train a baseline model (XGBoost) with default pipeline
ariel train --model xgboost --target SalePrice

# Produce a submission file
ariel submit --model xgboost --output submissions/pred.csv
```

**Expected output (excerpt):**
```
[INFO] 2026-06-02 12:34:56 | Initialized project at ./house-prices-advanced-regression-techniques
[INFO] 2026-06-02 12:35:01 | Downloaded 5 files (≈ 30 MB) to data/raw/
[INFO] 2026-06-02 12:35:45 | EDA report saved to reports/eda.html
[INFO] 2026-06-02 12:36:12 | Trained XGBoostRegressor (RMSE: 0.1243) on 1460 rows
[INFO] 2026-06-02 12:36:20 | Submission written to submissions/pred.csv
```

---

## 🔧 Configuration
Ariel_kaggle reads a YAML configuration file (`ariel.yaml`) placed at the project root. All keys are optional; missing values fall back to sensible defaults.

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `kaggle_competition` | `str` | *required* | Name of the Kaggle competition (e.g., `house-prices-advanced-regression-techniques`). |
| `data.raw_dir` | `str` | `data/raw` | Directory where raw CSVs are stored. |
| `data.processed_dir` | `str` | `data/processed` | Directory for cleaned/engineered datasets. |
| `pipeline.steps` | `list[str]` | `["impute", "encode", "scale"]` | Ordered list of preprocessing steps. |
| `model.type` | `str` | `xgboost` | Model identifier (`xgboost`, `lightgbm`, `sklearn.linear_model.Ridge`, etc.). |
| `model.params` | `dict` | `{}` | Hyper‑parameters passed to the model constructor. |
| `submission.file_name` | `str` | `submission_{timestamp}.csv` | Pattern for generated submission files. |
| `logging.level` | `str` | `INFO` | Logging verbosity (`DEBUG`, `INFO`, `WARNING`, `ERROR`). |
| `logging.format` | `str` | `json` | Log output format (`json` or `plain`). |

Example `ariel.yaml`:

```yaml
kaggle_competition: house-prices-advanced-regression-techniques
data:
  raw_dir: data/raw
  processed_dir: data/processed
pipeline:
  steps:
    - impute
    - encode
    - scale
model:
  type: xgboost
  params:
    n_estimators: 500
    max_depth: 6
submission:
  file_name: submission_{timestamp}.csv
logging:
  level: INFO
  format: json
```

---

## 📚 API Reference / Usage
Below are the most frequently used public classes and functions.

### `ariel.kaggle.KaggleService`
```python
from ariel.kaggle import KaggleService

svc = KaggleService(competition="house-prices-advanced-regression-techniques")
svc.download(path="data/raw")
svc.submit(file_path="submissions/pred.csv", message="baseline")
```
- **Methods**: `download()`, `submit()`, `list_submissions()`

### `ariel.pipeline.PipelineService`
```python
from ariel.pipeline import PipelineService

pipeline = PipelineService(steps=["impute", "encode", "scale"])
X_train, X_test = pipeline.fit_transform(train_df, test_df)
```
- **Supported steps**: `impute`, `encode`, `scale`, `pca`, `feature_hash`.

### `ariel.model.ModelWrapper`
```python
from ariel.model import ModelWrapper

model = ModelWrapper(
    model_type="xgboost",
    params={"n_estimators": 300, "learning_rate": 0.05}
)
model.fit(X_train, y_train)
preds = model.predict(X_test)
```
- **Attributes**: `model`, `params`, `fit_time`.
- **Methods**: `fit()`, `predict()`, `score(metric="rmse")`.

### CLI entry point
```bash
ariel [COMMAND] [OPTIONS]
```
| Command | Description |
|---------|-------------|
| `init <competition>` | Scaffold a new project directory. |
| `download` | Pull competition files via Kaggle API. |
| `eda` | Generate a pandas‑profiling HTML report. |
| `train` | Run the full training pipeline. |
| `submit` | Upload the latest CSV to Kaggle. |
| `config` | Show the effective configuration. |

---

## 🗂️ Project Structure
```
Ariel_kaggle/
├─ ariel/                     # Core package
│  ├─ __init__.py
│  ├─ cli.py                  # Typer CLI definitions
│  ├─ kaggle.py               # KaggleService implementation
│  ├─ pipeline.py             # FeaturePipeline utilities
│  ├─ model.py                # ModelWrapper abstraction
│  └─ utils.py                # Helper functions (logging, timestamps)
├─ tests/                     # Unit and integration tests
│  ├─ test_kaggle.py
│  ├─ test_pipeline.py
│  └─ test_model.py
├─ examples/                  # Minimal notebooks & scripts
│  └─ baseline.ipynb
├─ ariel.yaml                 # Default configuration (generated by `ariel init`)
├─ README.md                  # ← You are here
├─ LICENSE                    # MIT License
└─ pyproject.toml             # Build system & metadata
```

- **`ariel/cli.py`** – Parses commands and forwards to service objects.  
- **`ariel/kaggle.py`** – Thin wrapper around the official Kaggle CLI, providing Pythonic error handling.  
- **`ariel/pipeline.py`** – Constructs `sklearn.pipeline.Pipeline` objects from the YAML step list.  
- **`ariel/model.py`** – Dynamically imports the requested model class and stores training metadata.  

---

## 🧪 Running Tests
The test suite uses **pytest** and requires the optional `test` extra.

```bash
# Install test dependencies
pip install "ariel-kaggle[test]"

# Run the full suite
pytest -vv
```

For a quick sanity check (no external Kaggle calls):

```bash
pytest tests/test_pipeline.py::test_impute_step -q
```

---

## 🤝 Contributing
We welcome contributions of any size! Follow these steps to get started:

1. **Fork** the repository and clone your fork.
   ```bash
   git clone https://github.com/<your‑username>/Ariel_kaggle.git
   cd Ariel_kaggle
   ```
2. **Create a feature branch**:
   ```bash
   git checkout -b feat/awesome‑new‑step
   ```
3. **Install the development environment**:
   ```bash
   pip install -e ".[dev]"
   ```
4. **Make your changes**, ensuring they adhere to the project's **PEP 8** style (run `ruff` or `flake8`).  
5. **Add tests** covering new functionality; aim for ≥ 80 % coverage.  
6. **Run the full test suite** (`pytest -vv`).  
7. **Commit** with a clear message and **push** to your fork.
8. Open a **Pull Request** against `main`. Fill the PR template, reference any related issues, and wait for a maintainer review.

*Code of Conduct*: Be respectful, inclusive, and constructive. Harassment or discriminatory language will not be tolerated.

---

## 📄 License
This project is licensed under the **MIT License** – see the `LICENSE` file for details.