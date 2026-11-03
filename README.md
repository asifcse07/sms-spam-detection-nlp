# SMS Spam Detection with Basic NLP

Project status: Experiment complete — see experiment.ipynb (literature_review.md, methodology.md)

## Overview

This project studies whether short SMS text messages can be reliably classified as
**spam** or **ham** (legitimate) using basic NLP techniques covered in class:
text cleaning, bag-of-words / TF-IDF features, and classic classifiers
(Multinomial Naive Bayes, Logistic Regression).

## Dataset

[SMS Spam Collection](https://archive.ics.uci.edu/dataset/228/sms+spam+collection)
from the UCI Machine Learning Repository — 5,574 English SMS messages labelled
`ham` or `spam`. See [`data/README.md`](data/README.md) for download instructions.

## Repository Structure

```
.
├── data/            # dataset instructions (raw data is git-ignored)
├── proposal.md      # research proposal (added on the `proposal` branch)
├── literature_review.md  # summaries of 5 related papers
├── methodology.md   # refined question, data, features, models, metrics
├── experiment.ipynb # cleaning, features, model training and evaluation
├── CONFLICT_NOTES.md
└── README.md
```

## Technique

Basic NLP + classification (bag-of-words / TF-IDF, Naive Bayes, Logistic Regression),
evaluated with precision, recall, F1 and confusion matrices.
