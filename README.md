# UIT-VSFC Sentiment Analysis

Vietnamese student feedback sentiment classification.

## Team Members
| Name | Student ID | Approach |
|------|------------|----------|
| Nguyễn Đức Minh | 24022404 | Approach 1: SVM + XGBoost Ensemble |
| Đỗng Mạnh Hùng | 23020370 | Approach 2: phoBERT |
| Lương Minh Trí | 23020440 | Approach 2: phoBERT |

## Dataset
UIT-VSFC (Vietnamese Students' Feedback Corpus)
- 11,426 training sentences
- 1,583 dev sentences
- 3,166 test sentences
- Classes: Negative (0), Neutral (1), Positive (2)

## Baseline
MaxEnt (Nguyen et al., 2018): 87.94% F1-score

## Results

### Approach 1: SVM + XGBoost Ensemble
| Metric | Negative | Neutral | Positive | Overall |
|--------|----------|---------|----------|---------|
| Precision | 88.65% | 51.30% | 93.78% | **89.26%** |
| Recall | 94.82% | 35.33% | 91.07% | **89.80%** |
| F1-Score | 91.63% | 41.84% | 92.41% | **89.39%** |

**Improvement:** +1.45% F1 vs baseline (87.94% → 89.39%)

**Key Achievements:**
- Overall F1: 89.39% (+1.45% vs baseline)
- Neutral F1: 41.84% (+7.85% vs baseline 33.99%)
- Negative F1: 91.63% (+1.11% vs baseline 90.52%)
- Positive F1: 92.41% (+1.09% vs baseline 91.32%)

**Method:**
- **Preprocessing:** Word segmentation using Underthesea
- **Data Augmentation:** Random word swap & deletion to address class imbalance
  - Original: Negative 5,325 (46.6%), Neutral 458 (4.0%), Positive 5,643 (49.4%)
  - Augmented: Negative 5,325 (44.5%), Neutral 994 (8.3%), Positive 5,643 (47.2%)
  - Total training samples: 11,426 → 11,962
- **Feature Engineering:** TF-IDF vectorization
  - Word-level n-grams (1-2): 13,553 features
  - Character-level n-grams (3-5): 10,000 features
  - Combined: 23,553 features
- **Model 1 - Linear SVM:**
  - C = 0.5 (regularization parameter)
  - Class weights: Balanced (Neutral weight = 4.011)
  - Calibrated for probability estimates
- **Model 2 - XGBoost:**
  - max_depth = 6
  - n_estimators = 300
  - learning_rate = 0.1
  - Sample weights: Balanced
- **Ensemble:** Soft voting (SVM weight = 0.7, XGBoost weight = 0.3)

### Approach 2: PhoBERT
| Metric     | Negative | Neutral | Positive | Overall |
|------------|----------|---------|----------|---------|
| Precision  | 94.55%   | 59.17%  | 95.20%   | **93.01%** |
| Recall     | 94.82%   | 59.88%  | 94.84%   | **92.99%** |
| F1-Score   | 94.68%   | 59.52%  | 95.02%   | **93.00%** |

**Improvement:** +5.06% F1 (weighted) vs baseline (87.94% → 93.00%); Neutral F1 up from 33.99% → 59.52%.

**Method:**
- Model: Fine-tuned vinai/phobert-base with class weights from training distribution (to handle ~4% Neutral)
- Inputs: max_length=128, CLS embedding → dropout → linear classifier
- Training: AdamW (lr=2e-5), 4 epochs, batch size 32, linear warmup/decay (10% warmup), grad clip=1.0
- Early selection: best checkpoint by dev macro-F1; report per-class and overall (weighted + macro) on test

## Files
- **Slides:** [Canva Presentation](https://www.canva.com/design/DAG8IfMiPu8/ihY7Psrs46EdXH87Uwflig/edit?utm_content=DAG8IfMiPu8&utm_campaign=designshare&utm_medium=link2&utm_source=sharebutton)
- **Notebooks:**
  - `00_preprocessing.ipynb` - Data preprocessing pipeline
  - `approach_1_svm_xgboost_ensemble.ipynb` - Approach 1 implementation
  - `approach_2_1_phoBERT.ipynb` - Approach 2 implementation
  - `approach_2_2_data_processing_phoBERT.ipynb` - Approach 2 data processing

## Reference
Nguyen et al. (2018). UIT-VSFC: Vietnamese Students' Feedback Corpus for Sentiment Analysis. KSE 2018.
