# AI Security Project — SMS Spam Classification
### Adversarial Attacks & Defenses on a Text Classification Model

---

## Overview

This project investigates the robustness of a machine learning text classifier against adversarial attacks, and evaluates defense techniques to restore and improve model performance.

**Dataset:** [SMS Spam Collection](https://www.kaggle.com/datasets/uciml/sms-spam-collection-dataset) — 5,572 labeled English SMS messages (ham / spam)  
**Task:** Binary text classification  
**Model:** Logistic Regression with TF-IDF feature extraction

---

## Project Structure

```
project/
│
├── data/
│   └── spam.csv                        # Raw SMS Spam Collection dataset
│
├── notebooks/
│   ├── member1_data_baseline.ipynb     # Phase 1 — Data exploration & baseline model
│   └── member_attacks_defenses_v2.ipynb # Phases 2 & 3 — Attacks & defenses
│
├── results/
│   ├── baseline_results.csv            # Baseline model metrics
│   ├── processed_dataset.csv           # Cleaned & encoded dataset
│   ├── train_split.csv                 # Training split
│   ├── test_split.csv                  # Test split
│   └── attacks_defenses_results_v2.csv # Full comparison table
│
├── models/
│   ├── baseline_model.pkl              # Trained Logistic Regression model
│   └── tfidf_vectorizer.pkl            # Fitted TF-IDF vectorizer
│
├── plots/
│   ├── class_distribution.png
│   ├── confusion_matrix_baseline.png
│   ├── attacks_comparison_v2.png
│   ├── confusion_matrices_v2.png
│   └── journey_v2.png
│
└── README.md
```

---

## Phase 1 — Dataset & Baseline Model

**Notebook:** `member1_data_baseline.ipynb`

### Dataset
| Property | Value |
|---|---|
| Total samples | 5,572 |
| Ham messages | 4,825 (86.6%) |
| Spam messages | 747 (13.4%) |
| Language | English |
| Class imbalance | ~6.5× more ham than spam |

### Pipeline
1. **Preprocessing** — Lowercase, remove URLs, phone numbers, non-alphabetic characters
2. **Feature extraction** — TF-IDF with unigrams + bigrams, 10,000 features, sublinear TF
3. **Model** — Logistic Regression with GridSearchCV (tuning `C`, `penalty`, `class_weight`)
4. **Split** — 80/20 stratified train/test split

### Baseline Results
| Metric | Score |
|---|---|
| Accuracy | 0.9821 |
| Precision | 0.9388 |
| Recall | 0.9262 |
| F1-Score | 0.9324 |

---

## Phase 2 — Adversarial Attacks

**Notebook:** `member_attacks_defenses_v2.ipynb`

Both attacks are **black-box** — they manipulate raw text without accessing model weights or gradients.

---

### Attack 1 — Strong Homoglyph / Character Substitution

**Idea:** Replace common letters with visually similar symbols across 90% of words. This is a technique real spammers use to bypass keyword filters. The resulting tokens are out-of-vocabulary in TF-IDF, so spam keywords receive zero weight and the model fails to recognize them.

**Substitution map:**

| `a → @` | `e → 3` | `i → 1` | `o → 0` | `s → $` | `t → +` | `l → \|` | `g → 9` |
|---|---|---|---|---|---|---|---|

**Example:**
```
ORIGINAL : "free entry win prize call now"
ATTACKED : "fr33 3n+ry w1n pr1z3 c@|| n0w"
```

**Impact:**
| Metric | Baseline | After Attack 1 | Drop |
|---|---|---|---|
| Accuracy | 0.9821 | 0.8619 | −12.02% |
| Precision | 0.9388 | 0.4419 | −49.69% |
| Recall | 0.9262 | 0.1275 | **−79.87%** |
| F1-Score | 0.9324 | 0.1979 | **−73.45%** |

> The model misses nearly **9 out of 10 spam messages** after this attack.

---

### Attack 2 — Targeted Keyword Deletion

**Idea:** Extract the model's **top-50 highest-coefficient TF-IDF features** — the exact words that push predictions toward spam — and delete them from every message. Rather than random noise, this is a surgical strike on the model's most discriminative evidence.

**Top spam keywords removed (sample):** `free`, `call`, `win`, `prize`, `sexy`, `ringtone`, `stop`, `claim`, ...

**Example:**
```
ORIGINAL : "you have won a free prize call to claim now"
ATTACKED : "you have a to now"   ← spam keywords deleted
```

**Impact:**
| Metric | Baseline | After Attack 2 | Drop |
|---|---|---|---|
| Accuracy | 0.9821 | 0.9345 | −4.75% |
| Precision | 0.9388 | 1.0000 | +6.12% |
| Recall | 0.9262 | 0.5101 | **−41.61%** |
| F1-Score | 0.9324 | 0.6756 | **−25.69%** |

> Precision rises to 1.0 while Recall collapses — the model only predicts spam when extremely confident, but misses **half of all spam**. The model is not broken, it is blinded.

---

## Phase 3 — Defense Techniques

---

### Defense 1 — Extended Character Normalization

**Targets:** Attack 1

**Idea:** Extend the preprocessing pipeline with a **reverse homoglyph mapping** step applied before TF-IDF vectorization. Every substituted character is restored to its original letter before tokenization, so the vectorizer sees clean, known tokens again.

```python
REVERSE_MAP = {'@':'a', '3':'e', '1':'i', '0':'o',
               '$':'s', '+':'t', '|':'l', '9':'g'}
```

Applied consistently to both training and test data. A new vectorizer and model are re-fitted on the normalized corpus.

**Result vs. Attack 1:**
| Metric | After Attack 1 | After Defense 1 | Recovery |
|---|---|---|---|
| Accuracy | 0.8619 | **0.9821** | +12.02% |
| Precision | 0.4419 | **0.9388** | +49.69% |
| Recall | 0.1275 | **0.9262** | +79.87% |
| F1-Score | 0.1979 | **0.9324** | +73.45% |

> Defense 1 **fully restores** all metrics to exactly the original baseline.

---

### Defense 2 — Adversarial Training

**Targets:** Attack 1 + Attack 2

**Idea:** Rather than reversing the attack in preprocessing, make the model inherently robust by training it on a diverse augmented corpus that includes attacked examples. The training set is tripled:

| Split | Content | Size |
|---|---|---|
| Original | Clean training data | 4,457 |
| + Attack 1 | Homoglyph-corrupted versions | 4,457 |
| + Attack 2 | Keyword-deleted versions | 4,457 |
| **Total** | | **13,371** |

Character normalization is also applied throughout, combining both defenses.

**Results:**
| Metric | Baseline | vs. Attack 1 | vs. Attack 2 |
|---|---|---|---|
| Accuracy | 0.9821 | **0.9848** | **0.9686** |
| Precision | 0.9388 | **0.9521** | **0.9597** |
| Recall | 0.9262 | **0.9329** | **0.7987** |
| F1-Score | 0.9324 | **0.9424** | **0.8718** |

> Defense 2 **exceeds the baseline** against Attack 1, and substantially recovers from Attack 2 (F1: 0.6756 → 0.8718), demonstrating genuine generalization to unseen perturbations.

---

## Full Results Summary

| Step | Accuracy | Precision | Recall | F1-Score |
|---|---|---|---|---|
| Baseline | 0.9821 | 0.9388 | 0.9262 | 0.9324 |
| After Attack 1 (Homoglyph) | 0.8619 | 0.4419 | 0.1275 | 0.1979 |
| After Attack 2 (Keyword Del) | 0.9345 | 1.0000 | 0.5101 | 0.6756 |
| Defense 1 — Char Norm (vs Atk1) | 0.9821 | 0.9388 | 0.9262 | 0.9324 |
| Defense 2 — Adv Training (vs Atk1) | 0.9848 | 0.9521 | 0.9329 | 0.9424 |
| Defense 2 — Adv Training (vs Atk2) | 0.9686 | 0.9597 | 0.7987 | 0.8718 |

---

## Requirements

```
pandas
numpy
scikit-learn
matplotlib
seaborn
```

Install with:
```bash
pip install pandas numpy scikit-learn matplotlib seaborn
```

---

## How to Run

1. Place `spam.csv` in the `data/` folder
2. Run `member1_data_baseline.ipynb` — trains and saves the baseline model
3. Run `member_attacks_defenses_v2.ipynb` — applies attacks and defenses, generates all plots and result CSVs
