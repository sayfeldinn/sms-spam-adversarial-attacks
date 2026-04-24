# AI Security Issues Final Project Plan
## Project Title
Adversarial Attacks on Text-Based Models
## Course
AI Security Issues  
Instructor: Eng. Abdelrahman Shehata
## Team
- Member 1 — Moatasem Mohamed
- Member 2 — Seif Eldeen Nasser
- Member 3 — Ahmed Refaat


---
## 1. Project Objective
The goal of this project is to build a text classification model, test it against adversarial attacks, and apply defense techniques to improve its robustness.
The project follows the full AI security workflow:
1. Build and evaluate a text classification model
2. Apply two adversarial attacks
3. Apply two defense techniques
4. Compare results before attack, after attack, and after defense
---
## 2. Selected Dataset

**Dataset:** [SMS Spam Collection Dataset ](https://www.kaggle.com/datasets/uciml/sms-spam-collection-dataset) 
**Task:** Spam vs Ham text classification
**Reason for selection:**
- Small and easy to preprocess
- Suitable for binary text classification
- Easy to demonstrate adversarial text attacks clearly
- Faster to train and test compared to larger datasets
Dataset link:  

---
## 3. Proposed Model
We will build a baseline text classification model using:
- **TF-IDF vectorization**
- **Logistic Regression** or **Linear SVM**

This model is suitable because:
- It works well for text classification
- It is easy to explain in the report
- It allows clear observation of attack impact and defense improvement
  
---

## 4. Evaluation Metrics
The model will be evaluated using the following metrics:
- **Accuracy**
- **Precision**
- **Recall**
- **F1-Score**

These metrics will be reported for:
1. Original model
2. Model after adversarial attacks
3. Model after defense techniques

---

## 5. Planned Adversarial Attacks
We will apply **two different adversarial attacks** to the text classifier.

### Attack 1: Synonym Replacement Attack
Important words in the message will be replaced with similar words to change the text while preserving the meaning.

**Example idea:**
- `free prize now` -> `complimentary reward now`

**Expected effect:**
- Reduce model confidence
- Cause misclassification of spam as ham or vice versa

### Attack 2: Character-Level Typo Attack
Words will be modified using typos, character replacement, spacing changes, or symbol insertion.

**Example idea:**
- `free offer` -> `fr3e off3r`
- `win now` -> `w1n n0w`

**Expected effect:**
- Break token patterns learned by the classifier
- Lower classification performance

---

## 6. Planned Defense Techniques
We will apply **two defense techniques** to improve robustness.

### Defense 1: Adversarial Training
Adversarially modified samples will be added to the training data, then the model will be retrained.

**Expected benefit:**
- Help the model learn attacked patterns
- Improve robustness against similar attacks

### Defense 2: Text Normalization / Input Cleaning
Preprocessing will be strengthened before classification.
This may include:
- lowercasing
- removing extra symbols
- correcting repeated characters
- normalizing noisy words
**Expected benefit:**
- Reduce the impact of typo-based attacks
- Improve model stability on noisy inputs
---
## 7. Team Roles and Task Division
### Member 1 (Moatasem) — Data & Baseline Model Lead

#### Tasks
- Download and load the dataset
- Explore the dataset and describe class distribution
- Clean and preprocess the text
- Split data into training and testing sets
- Train the baseline model
- Evaluate the model using accuracy, precision, recall, and F1-score
- Create baseline result tables and visualizations
#### Deliverables
- Dataset description section
- Preprocessing section
- Baseline model training and evaluation
- Original model performance results
---
### Member 2 (Seif Eldeen) — Adversarial Attacks Lead
**Main responsibility:** Implement and test attacks
#### Tasks
- Research and select two attack methods
- Implement synonym replacement attack
- Implement character-level typo attack
- Generate attacked test samples
- Compare predictions before and after attack
- Measure model performance after each attack
- Prepare examples showing successful adversarial changes
#### Deliverables
- Attack implementation section
- Before/after text examples
- Performance results after attack
- Attack comparison table
---
### Member 3 (Ahmed Refaat) — Defense & Documentation Lead
**Main responsibility:** Improve robustness and prepare final submission
#### Tasks
- Research and implement two defense techniques
- Apply adversarial training
- Apply text normalization / cleaning defense
- Re-evaluate the defended model
- Create final comparison tables and charts
- Combine all sections into the final notebook
- Write and format the PDF report
- Prepare the final presentation/discussion points
#### Deliverables
- Defense implementation section
- Final evaluation after defense
- Result comparison charts
- PDF report and submission organization
---
## 8. Shared Responsibilities
All team members will work together on the following:
- Decide final tools and model choice
- Review notebook structure and code quality
- Check that the notebook runs from start to finish without errors
- Review the report before submission
- Practice project explanation for the discussion session
---
## 9. Workflow Plan
### Phase 1: Data Preparation and Baseline Model
- Load dataset
- Clean and preprocess text
- Train the classifier
- Record baseline metrics
### Phase 2: Adversarial Attacks
- Apply Attack 1
- Apply Attack 2
- Record performance changes
- Save examples of prediction changes
### Phase 3: Defense Techniques
- Apply Defense 1
- Apply Defense 2
- Retrain or update preprocessing pipeline
- Re-evaluate the model
### Phase 4: Comparison and Reporting
- Compare:
  - Before Attack
  - After Attack
  - After Defense
- Prepare tables, figures, and conclusions
- Finalize notebook and PDF report
---
## 10. Expected Deliverables
### 1. Code Notebook
Must include:
- Dataset loading
- Preprocessing
- Model training
- Evaluation metrics
- Two attacks
- Two defenses
- Final performance comparison
### 2. PDF Report
Must include:
- Project objective
- Dataset description
- Preprocessing steps
- Model design
- Evaluation metrics
- Attack methods
- Defense techniques
- Visual examples
- Result comparison
- Final conclusion
---
## 11. Final Comparison Table Template
| Stage | Accuracy | Precision | Recall | F1-Score |
|------|------:|------:|------:|------:|
| Before Attack | - | - | - | - |
| After Attack 1 | - | - | - | - |
| After Attack 2 | - | - | - | - |
| After Defense 1 | - | - | - | - |
| After Defense 2 | - | - | - | - |

