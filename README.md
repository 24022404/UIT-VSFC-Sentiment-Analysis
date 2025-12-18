# UIT-VSFC Sentiment Analysis

Vietnamese student feedback sentiment classification.

## Team Members
| Name | Student ID | Approach |
|------|------------|----------|
| Nguyễn Đức Minh | 24022404 | Approach 1: SVM + XGBoost Ensemble |
| Đỗng Mạnh Hùng | 23020370 | Approach 2: phoBERT |
| Lương Minh Trí | 23020440 | Approach 3 |

## Dataset
UIT-VSFC (Vietnamese Students' Feedback Corpus)
- 11,426 training sentences
- 3,166 test sentences
- Classes: Negative, Neutral, Positive

## Baseline
MaxEnt (Nguyen et al., 2018): 87.94% F1-score

## Results

### Approach 1: SVM + XGBoost Ensemble
| Metric | Negative | Neutral | Positive | Overall |
|--------|----------|---------|----------|---------|
| Precision | 88.76% | 55.37% | 94.03% | **89.65%** |
| Recall | 94.75% | 40.12% | 91.13% | **90.05%** |
| F1-Score | 91.66% | 46.53% | 92.56% | **89.73%** |

**Improvement:** +1.79% F1 vs baseline

**Method:**
- TF-IDF features (word 1-2 grams + char 3-5 grams)
- Data augmentation (Neutral class: 4% → 8.5%)
- Soft voting ensemble (SVM=0.7, XGB=0.3)

### Approach 2
| Metric     | Negative | Neutral | Positive | Overall |
|------------|----------|---------|----------|---------|
| Precision  | 94.55%   | 59.17%  | 95.20%   | **93.01%** |
| Recall     | 94.82%   | 59.88%  | 94.84%   | **92.99%** |
| F1-Score   | 94.68%   | 59.52%  | 95.02%   | **93.00%** |

**Improvement:** +5.06% F1 (weighted) vs baseline (87.94% → 93.00%); Neutral F1 up from 33.99% → 59.52%.

**Method:**
Model: Fine-tuned vinai/phobert-base with class weights from training distribution (to handle ~4% Neutral).
Inputs: max_length=128, CLS embedding → dropout → linear classifier.
Training: AdamW (lr=2e-5), 4 epochs, batch size 32, linear warmup/decay (10% warmup), grad clip=1.0.
Early selection: best checkpoint by dev macro-F1; report per-class and overall (weighted + macro) on test.

### Approach 3
Coming soon

## Reference
Nguyen et al. (2018). UIT-VSFC: Vietnamese Students' Feedback Corpus for Sentiment Analysis. KSE 2018.
