# Project Proposal: SMS Spam Detection with Basic NLP

## 1. Research Question

**Main question:** How accurately can basic NLP features (bag-of-words and TF-IDF)
combined with simple, interpretable classifiers detect spam in short SMS messages?

**Sub-questions:**

1. Does TF-IDF weighting outperform raw word counts for this task?
2. Which performs better on this imbalanced dataset (≈13% spam):
   Multinomial Naive Bayes or Logistic Regression?
3. Which words/tokens are the strongest indicators of spam?

**Why it matters:** SMS spam is a common vector for fraud and phishing. Because
messages are short and full of abbreviations, it is useful to know how far
lightweight, explainable models can go before heavier deep-learning approaches are needed.

## 2. Dataset

| Item | Details |
|------|---------|
| Name | SMS Spam Collection |
| Source | UCI Machine Learning Repository |
| Link | https://archive.ics.uci.edu/dataset/228/sms+spam+collection |
| Creators | Tiago A. Almeida, José María Gómez Hidalgo |
| Size | 5,574 English SMS messages (4,827 ham, 747 spam) |
| Format | Tab-separated: `label` (`ham`/`spam`), `message` (raw text) |
| License | CC BY 4.0 |

The data was gathered from several public sources, including the Grumbletext
UK forum (spam reports), the NUS SMS Corpus, and Caroline Tag's PhD thesis.

## 3. Scan of Existing Work

### Source 1 — Almeida, Gómez Hidalgo & Yamakami (2011)

T. A. Almeida, J. M. Gómez Hidalgo, A. Yamakami. *"Contributions to the Study of
SMS Spam Filtering: New Collection and Results."* Proceedings of the 11th ACM
Symposium on Document Engineering (DocEng '11), pp. 259–262, 2011.
https://doi.org/10.1145/2034691.2034742

- Introduces the SMS Spam Collection, the dataset used in this project.
- Benchmarks many classic classifiers (several Naive Bayes variants, C4.5,
  k-NN, SVM, Boosted NB, etc.) on tokenized message text.
- Finds that a **linear SVM** gave the best overall results, with high accuracy
  while blocking most spam and keeping false positives (blocked ham) low.
- **Relevance:** gives baseline numbers to compare against and confirms that
  simple token-based features work well for SMS spam.

### Source 2 — Gómez Hidalgo, Cajigas Bringas, Puertas Sanz & Carrero García (2006)

J. M. Gómez Hidalgo, G. Cajigas Bringas, E. Puertas Sanz, F. Carrero García.
*"Content Based SMS Spam Filtering."* Proceedings of the 2006 ACM Symposium on
Document Engineering (DocEng '06), pp. 107–114, 2006.
https://doi.org/10.1145/1166160.1166191

- One of the first studies to apply content-based (email-style) spam filtering
  to SMS, using English and Spanish test collections.
- Compares Bayesian filters and other learners with different tokenization
  strategies (words, character n-grams).
- Shows that **Bayesian filtering techniques transfer effectively** to SMS,
  despite the short message length.
- **Relevance:** motivates using Naive Bayes as a strong baseline and
  experimenting with tokenization choices.

### Gap this project addresses

Both studies predate today's standard Python tooling and report results with
their own feature pipelines. This project will reproduce a clean, fully
documented **scikit-learn** pipeline (counts vs. TF-IDF × Naive Bayes vs. Logistic
Regression), report class-imbalance-aware metrics, and inspect which tokens drive
predictions.

## 4. Methodology (planned)

1. **Preprocessing:** lowercase, remove punctuation/numbers (also test keeping
   them), remove stop-words, optional stemming.
2. **Features:** `CountVectorizer` and `TfidfVectorizer` (unigrams, then uni+bigrams).
3. **Models:** Multinomial Naive Bayes, Logistic Regression.
4. **Validation:** stratified 80/20 train/test split + 5-fold stratified
   cross-validation on the training set for hyperparameters.
5. **Metrics:** precision, recall, F1 (spam class), ROC-AUC, confusion matrix.
   Accuracy alone is misleading because only ≈13% of messages are spam.
6. **Interpretation:** top-weighted tokens per class.

## 5. Expected Outcome

A reproducible notebook comparing the four feature/model combinations, with
F1 on the spam class expected around 0.90 or higher, plus a short discussion of
error cases (e.g., ham messages wrongly flagged as spam).

## 6. Timeline

| Week | Task |
|------|------|
| 1 | Proposal, data download, EDA |
| 2 | Preprocessing and feature extraction |
| 3 | Model training and evaluation |
| 4 | Interpretation, write-up, final report |
