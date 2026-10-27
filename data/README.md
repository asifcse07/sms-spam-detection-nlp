# Data

## Source

- **Name:** SMS Spam Collection
- **Publisher:** UCI Machine Learning Repository
- **Link:** https://archive.ics.uci.edu/dataset/228/sms+spam+collection
- **Creators:** Tiago A. Almeida & José María Gómez Hidalgo (2011)
- **License:** CC BY 4.0

## Description

5,574 English SMS messages, each tagged as `ham` (4,827) or `spam` (747).
The file `SMSSpamCollection` is tab-separated with two columns: `label` and `message`.

## How to download

```bash
curl -L -o data/raw/sms_spam.zip https://archive.ics.uci.edu/static/public/228/sms+spam+collection.zip
unzip data/raw/sms_spam.zip -d data/raw/
```

Raw files in `data/raw/` are git-ignored to keep the repository small.
