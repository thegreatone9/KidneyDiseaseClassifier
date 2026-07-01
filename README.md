# Kidney Disease Classification

A deep learning project that classifies kidney CT scan images as **Normal** or **Tumor** using a VGG16-based CNN, with full MLOps tooling and a Flask web application for real-time inference.

---

## Overview

This project builds an end-to-end deep learning pipeline for binary classification of kidney CT scan images. It leverages transfer learning with VGG16 (pretrained on ImageNet), tracks experiments with MLflow, and serves predictions through a Flask web application. The codebase follows a modular, config-driven architecture designed for maintainability and extensibility.

## What Problem This Project Solves

- Kidney tumors are a serious health concern, and early detection through CT scans is critical for successful treatment outcomes.
- Manual analysis of CT scan images is time-consuming, subjective, and prone to human error -- especially under high case volumes.
- This project automates the classification of kidney CT scan images into **Normal** vs **Tumor** categories using deep learning, enabling faster, more consistent, and more accessible screening.

## Key Implementation Steps

- **Data ingestion** from a public dataset of kidney CT scan images, downloaded and extracted automatically via `gdown`.
- **Transfer learning** using VGG16 (pretrained on ImageNet) as the base feature extractor.
- **Freezing all pre-trained convolutional layers** and adding a custom classification head: `Flatten -> Dense(2, softmax)`.
- **Training with data augmentation** -- rotation, horizontal/vertical flipping, width/height shifting, shear, and zoom -- to improve generalization.
- **Model evaluation** with loss and accuracy metrics on a held-out validation set.
- **Experiment tracking** with MLflow to log parameters, metrics, and model artifacts.
- **Pipeline orchestration** via `main.py` across 4 sequential stages.
- **Flask web application** for uploading CT scan images and receiving real-time Normal/Tumor predictions.

## Challenges We Faced

- **Balancing model complexity vs training time** with limited compute resources. The default configuration uses 1 epoch for demonstration purposes; increase this value in `params.yaml` for production-quality results.
- **Choosing the right transfer learning strategy.** Freezing all VGG16 layers provided a stable starting point but limited accuracy ceiling. Fine-tuning deeper layers would improve results at the cost of longer training and potential overfitting.
- **Managing large model files (~59 MB `.h5`)** in version control. Addressed by excluding binary artifacts via `.gitignore` and regenerating them with `python main.py`.
- **Structuring a modular, config-driven codebase** that cleanly separates concerns across components, pipelines, entity definitions, and configuration management.
- **Handling Google Drive download quirks** with `gdown` for automated, headless data ingestion during the pipeline's first stage.

## What We Learned

- How to build an **end-to-end ML pipeline** with proper MLOps practices -- from raw data to deployed model.
- The power of **transfer learning**: VGG16 features are surprisingly effective for medical image classification, even when all convolutional layers are frozen.
- **Config-driven pipelines** make ML workflows reproducible and easy to modify without touching code.
- **MLflow provides essential experiment tracking** -- logging parameters, metrics, and registering models for comparison and reproducibility.
- **Config-driven architecture** (YAML config files + Python dataclass entities) keeps ML codebases clean, testable, and maintainable.
- The importance of **separating experimentation (Jupyter notebooks) from production code** (modular Python packages with clear interfaces).

## Project Structure

```
├── app.py                      # Flask web server for inference
├── main.py                     # Runs all 4 pipeline stages sequentially
├── config/
│   └── config.yaml             # Paths, URLs, and data source configuration
├── params.yaml                 # Model hyperparameters (epochs, LR, augmentation, etc.)
├── src/cnnClassifier/
│   ├── components/             # Core ML logic (ingestion, model prep, training, eval)
│   ├── config/                 # Configuration manager (reads YAML, returns dataclasses)
│   ├── constants/              # File path constants (config paths)
│   ├── entity/                 # Dataclass definitions for typed configs
│   ├── pipeline/               # Stage orchestrators + prediction pipeline
│   └── utils/                  # Shared utility functions
├── templates/
│   └── index.html              # Web UI for uploading images
├── model/                      # Trained model artifact (.h5)
├── artifacts/                  # Pipeline outputs (data, base model, training results)
├── Dockerfile                  # Container configuration
├── requirements.txt            # Python dependencies
├── setup.py                    # Package setup
└── README.md
```

## Tech Stack

| Component            | Technology              |
| -------------------- | ----------------------- |
| Language             | Python 3.8              |
| Deep Learning        | TensorFlow / Keras      |
| Base Model           | VGG16 (ImageNet)        |
| Web Framework        | Flask                   |

| Experiment Tracking  | MLflow                  |
| Containerization     | Docker                  |
| Data Download        | gdown                   |

## Getting Started

### 1. Clone the Repository

```bash
git clone <repo-url>
cd Kidney-Disease-Classification-Deep-Learning-Project
```

### 2. Create and Activate the Conda Environment

```bash
conda create -n kidney python=3.8 -y
conda activate kidney
```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

### 4. Train the Model

Run all pipeline stages sequentially:

```bash
python main.py
```

### 5. Launch the Web Application

```bash
python app.py
```

Open your browser and navigate to `http://localhost:8082`. Upload a kidney CT scan image to receive a Normal or Tumor prediction.

### 6. View MLflow Experiment Tracking

Set the MLflow tracking URI environment variable, then start the UI:

```bash
export MLFLOW_TRACKING_URI=<your-tracking-uri>
mlflow ui
```

## Pipeline Stages

The training pipeline (`main.py`) runs 4 sequential stages:

| Stage | Name               | Description                                                                 |
| ----- | ------------------ | --------------------------------------------------------------------------- |
| 1     | Data Ingestion     | Downloads the kidney CT scan dataset from a remote source and extracts it   |
| 2     | Prepare Base Model | Loads VGG16 with frozen weights and attaches a custom classification head   |
| 3     | Training           | Trains the model with data augmentation and an 80/20 train/validation split |
| 4     | Evaluation         | Evaluates on the validation set, saves metrics, and logs results to MLflow  |



## License

This project is licensed under the MIT License.
