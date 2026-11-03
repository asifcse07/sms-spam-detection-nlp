# Methodology

## 1. Refined Research Question

**Can a TF-IDF text representation combined with a linear classifier (Logistic Regression or Linear SVM)
achieve a higher spam-class F1 score than a Multinomial Naive Bayes baseline on the SMS Spam Collection,
and do simple hand-crafted message features (length, digits, URLs, currency symbols, uppercase ratio)
add measurable value on top of the text features?**

Compared to the proposal, the question is narrowed to (a) one primary metric — F1 on the spam class —
and (b) a concrete test of whether a few structural features help beyond bag-of-words.

## 2. Dataset Description

| Item | Details |
|------|---------|
| Name / source | SMS Spam Collection, UCI ML Repository (https://archive.ics.uci.edu/dataset/228/sms+spam+collection) |
| Size | 5,574 messages (4,827 ham, 747 spam ≈ 13.4%); 5,171 after removing 403 exact duplicates (4,518 ham, 653 spam) |
| Format | Tab-separated, no header: `label`, `message` |
| Raw feature | `message` — free English SMS text (2–910 characters, median 62) |
| Target | `label` — `ham` (0) or `spam` (1) |
| License | CC BY 4.0 |

**Limitations**

- **Class imbalance:** only ~13% spam, so accuracy is a misleading metric.
- **Duplicates:** 403 exact duplicate rows (mostly repeated spam templates) can leak between train and test sets.
- **Age and region:** messages were collected around 2004–2011, mostly UK (spam) and Singapore (ham, NUS corpus);
  modern spam (URL shorteners, crypto, delivery scams) and other dialects are under-represented.
- **Source bias:** ham and spam come from different sources, so a model may partly learn source style
  (e.g. Singlish slang in ham) rather than "spamminess".
- **Small and English-only:** results may not generalise to other languages or larger, newer corpora.

## 3. Data Cleaning Plan

1. Load with `quoting=csv.QUOTE_NONE` so quote characters inside messages do not break parsing.
2. Check for missing values and empty messages (drop if any).
3. Remove exact duplicate `(label, message)` rows **before** splitting, to avoid train/test leakage.
4. Encode the target: `ham → 0`, `spam → 1`.
5. Text normalisation (applied inside the feature pipeline):
   - lowercase;
   - replace URLs with a `urltoken`, phone-like numbers (5+ digits) with `phonetoken`,
     currency amounts (`£`, `$`, `€`) with `moneytoken`, other numbers with `numtoken`
     — so the model learns "contains a phone number" rather than memorising specific numbers;
   - remove remaining punctuation and collapse whitespace.
6. Stratified 80/20 train/test split (`random_state=42`) so both sets keep the ~13% spam ratio.

## 4. Feature Engineering Plan

1. **Text features:** `TfidfVectorizer` on the cleaned text, unigrams + bigrams (`ngram_range=(1, 2)`),
   `min_df=2`, `sublinear_tf=True`, English stop words removed. A `CountVectorizer` version is used for the
   Naive Bayes baseline comparison.
2. **Hand-crafted structural features** (computed on the *raw* message, before cleaning):
   - `char_len` — number of characters;
   - `word_count` — number of words;
   - `digit_count` — number of digits;
   - `upper_ratio` — share of letters that are uppercase;
   - `has_url` — 1 if the message contains a URL / `www`;
   - `has_currency` — 1 if it contains `£`, `$` or `€`;
   - `exclaim_count` — number of `!`.
   These are scaled (`MaxAbsScaler`, which keeps them non-negative and sparse-friendly) and concatenated with
   the TF-IDF matrix using a `ColumnTransformer`.
3. All feature steps live inside scikit-learn `Pipeline`s, so they are fitted on the training data only.

## 5. Models and Why

| Model | Why |
|-------|-----|
| **Multinomial Naive Bayes** (count features) | Classic, fast spam-filter baseline; strong on short texts (sources 2 and 5 in the literature review). |
| **Logistic Regression** (TF-IDF + structural features, `class_weight="balanced"`) | Linear, interpretable (coefficients show spam words), handles mixed feature types and class imbalance. |
| **Linear SVM** (`LinearSVC`, TF-IDF + structural features, `class_weight="balanced"`) | Best performer in the original dataset paper (source 1/3); strong on high-dimensional sparse text. |

Key hyperparameters (`alpha` for NB, `C` for LR/SVM) are tuned with 5-fold stratified cross-validation
on the training set (`GridSearchCV`, scoring = F1). An ablation (Logistic Regression with vs. without the
structural features) answers the second half of the research question.

## 6. Evaluation Metrics and Why

- **Primary: F1 score on the spam class.** Balances precision and recall and is not inflated by the large ham class.
- **Precision (spam):** of messages flagged as spam, how many really are — low precision means legitimate
  messages get blocked, which is the costliest error for users.
- **Recall (spam):** share of spam caught.
- **Matthews Correlation Coefficient (MCC):** a single balanced score for imbalanced binary problems, used in the
  Almeida et al. papers, so results are comparable with the literature.
- **ROC-AUC:** threshold-independent ranking quality.
- **Confusion matrix:** to inspect false positives (ham → spam) and false negatives directly.
- Accuracy is reported only for reference, since predicting "ham" for everything already gives ~87%.

All final numbers are reported on the held-out 20% test set, which is never used for tuning.
