# 📱 Electronics Scam Detection System

A multimodal deep learning system that detects scam listings for mobile phones on e-commerce platforms (Jumia, Amazon, OLX). The system scrapes a product URL, analyzes the listing using text, image, and tabular models, and returns a scam probability score with a verdict.

---

## 🎯 What It Does

Paste any product listing URL → the system scrapes the page, runs it through three AI models, fuses the results, and tells you whether the listing is **Trusted**, **Suspicious**, or a **Scam**.

---

## 🏗️ Architecture

The system uses **two fusion sequences**, each combining one model per modality:

### Sequence A
| Modality | Model | Weight |
|----------|-------|--------|
| Text | BiLSTM | 40% |
| Image | ResNet50 | 35% |
| Tabular | XGBoost | 25% |

### Sequence B
| Modality | Model | Weight |
|----------|-------|--------|
| Text | TF-IDF + Logistic Regression | 40% |
| Image | EfficientNet-B0 | 35% |
| Tabular | Random Forest | 25% |

Each model outputs a scam probability (0–1). The fusion layer takes a weighted average to produce the **final score**:

| Score | Verdict | Risk Level |
|-------|---------|------------|
| ≥ 0.65 | Scam | High |
| 0.40 – 0.64 | Suspicious | Medium |
| < 0.40 | Trusted | Low |

---

## 📂 Project Structure

```
├── app.py                        # Streamlit UI (entry point)
├── Models/
│   ├── text_models/
│   │   ├── lstm_model.py         # BiLSTM text classifier
│   │   └── tfidf_model.py        # TF-IDF + Logistic Regression
│   ├── image_models/
│   │   ├── resnet50_model.py     # ResNet50 image classifier
│   │   └── efficientnet_model.py # EfficientNet-B0 image classifier
│   ├── ml_models/
│   │   ├── xgboost_model.py      # XGBoost tabular classifier
│   │   └── random_forest_model.py# Random Forest tabular classifier
│   └── fusion/
│       ├── fusion_model.py       # Sequence A (LSTM + ResNet50 + XGBoost)
│       └── fusion_model_b.py     # Sequence B (TF-IDF + EfficientNet + RF)
├── Data/
│   └── raw_data/
│       └── listings(Cleaned).csv # Dataset with text, image paths, labels
├── Notebooks/
│   └── Cleaning.ipynb            # EDA and data cleaning notebook
└── Documents/
    ├── Project_Documentation.docx
    └── Team_Guide.docx
```

---

## ⚙️ How It Works — Step by Step

1. **User pastes a URL** (Jumia, Amazon, or OLX)
2. **Scraper** extracts title, description, price, seller rating, and product image
3. **Image** is downloaded to a temp file for the image models
4. **Phone model** is extracted from the title via regex (iPhone, Samsung, Xiaomi, etc.)
5. **Three models** run on their respective modality and each return a scam score
6. **Fusion layer** computes a weighted average of the three scores
7. **Verdict** is displayed in the UI with a gauge chart and risk breakdown

---

## 🧠 Models & Performance

| Model | Accuracy | ROC-AUC |
|-------|----------|---------|
| BiLSTM | 81% | 0.931 |
| TF-IDF + LR | ~99% | ~1.00 |
| ResNet50 | ~95% | ~0.99 |
| EfficientNet-B0 | ~95% | ~0.99 |
| XGBoost | ~100% | ~1.00 |
| Random Forest | ~100% | ~1.00 |

> **Note:** The near-perfect accuracy on some models reflects structural patterns in the dataset (OLX listings are labeled scam, Jumia/Amazon listings are labeled trusted). The BiLSTM is the most generalizable model. This is acknowledged as a known limitation.

---

## 🚀 Getting Started

### Prerequisites

```bash
pip install streamlit torch torchvision scikit-learn xgboost joblib pandas Pillow requests beautifulsoup4
```

### Run the App

```bash
streamlit run app.py
```

Then open your browser at `http://localhost:8501`, paste a product URL, and click **Scan**.

---

## 📊 Dataset

- ~600 listings scraped from Jumia, Amazon Egypt, and OLX Egypt
- 50/50 split between trusted and scam listings
- Features: title, description, price, phone model, seller rating, image path, label
- Data cleaning and EDA available in `Notebooks/Cleaning.ipynb`

---

## 🛠️ Tech Stack

- **UI:** Streamlit
- **Deep Learning:** PyTorch (BiLSTM, ResNet50, EfficientNet-B0)
- **Machine Learning:** scikit-learn, XGBoost
- **Scraping:** BeautifulSoup, requests
- **Data:** pandas, NumPy

---

## 👥 Team

Built as a Deep Learning course project at Helwan National University — 6 team members, one model each.

---

## 📄 License

This project is for academic purposes.
