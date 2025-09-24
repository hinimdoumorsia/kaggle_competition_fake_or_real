# 📰 Fake or Real: The Impostor Hunt – Kaggle Competition

This repository contains my solution for the Kaggle competition  
**[Fake or Real: The Impostor Hunt](https://www.kaggle.com/competitions/fake-or-real-the-impostor-hunt)**.

## 🚀 Approach
- **Models:** Random Forest, XGBoost, SVM, and ensemble methods.
- **Text Representation:** GloVe embeddings (300d) + TF-IDF.
- **Preprocessing:** Tokenization, lowercasing, stopword removal.
- **Libraries:** `pandas`, `numpy`, `scikit-learn`, `nltk`, `xgboost`, `scipy`, `re`.

## 📊 Results
- Best public leaderboard score: **0.85** using hard voting ensemble.

## 📂 Project Structure

```plaintext
kaggle_competition_fake_or_real/
│
├── notebooks/             # Jupyter notebooks for training and evaluation
│   └── main_notebook.ipynb
│
├── glove/                 # Pretrained GloVe embeddings (300d)
│   └── glove.6B.300d.txt
│
├── README.md              # Project documentation           
