# Literature Review: SMS Spam Detection

Five sources that shaped the methodology of this project, each summarised in my own words.

---

### 1. Almeida, Gómez Hidalgo & Yamakami (2011) — *Contributions to the Study of SMS Spam Filtering: New Collection and Results*

Proceedings of the 11th ACM Symposium on Document Engineering (DocEng '11), pp. 259–262.
https://doi.org/10.1145/2034691.2034742

This paper introduces the SMS Spam Collection, the 5,574-message dataset used in this project, and explains
how it was assembled from public spam reports and existing ham corpora. The authors benchmark a long list of
classic classifiers on simple token features and find that a linear SVM gives the best balance between catching
spam and not blocking legitimate messages. For my work it provides the baseline numbers to compare against and
shows that plain word tokens are already a strong representation.

### 2. Gómez Hidalgo, Cajigas Bringas, Puertas Sanz & Carrero García (2006) — *Content Based SMS Spam Filtering*

Proceedings of the 2006 ACM Symposium on Document Engineering (DocEng '06), pp. 107–114.
https://doi.org/10.1145/1166160.1166191

One of the earliest studies to ask whether email-style content filters work on SMS, which is much shorter and
noisier than email. Testing on English and Spanish collections with several tokenisation schemes, the authors
show that Bayesian filters carry over well and that the choice of tokens (words vs. character n-grams) matters.
This motivates using Naive Bayes as my baseline and experimenting with n-gram ranges.

### 3. Almeida, Gómez Hidalgo & Silva (2013) — *Towards SMS Spam Filtering: Results under a New Dataset*

International Journal of Information Security Science, 2(1), pp. 1–18.

This is the extended journal version of the 2011 work, with a much larger set of experiments on the same
collection and more careful statistical comparison of the methods. It again finds linear SVMs and some Naive Bayes
variants at the top, and stresses using the Matthews Correlation Coefficient (MCC) and spam recall rather than
accuracy, because the classes are imbalanced. I adopt their point that accuracy is misleading here and report
class-aware metrics instead.

### 4. Delany, Buckley & Greene (2012) — *SMS Spam Filtering: Methods and Data*

Expert Systems with Applications, 39(10), pp. 9899–9908.
https://doi.org/10.1016/j.eswa.2012.02.053

A survey of SMS spam filtering that reviews the available datasets and the content-based and non-content
approaches used up to that point. The authors also build a cleaner dataset and find that, with good features,
fairly simple classifiers are hard to beat, and that duplicate/near-duplicate spam messages can inflate
reported results. This warned me to check for and remove duplicate messages before splitting into train and test.

### 5. Metsis, Androutsopoulos & Paliouras (2006) — *Spam Filtering with Naive Bayes – Which Naive Bayes?*

Third Conference on Email and Anti-Spam (CEAS 2006).

The paper compares five Naive Bayes variants (including multinomial with term frequencies and with boolean
attributes) on realistic, time-ordered email data. Multinomial NB and the boolean variants perform best, and the
paper shows that the choice of variant and feature representation affects results more than people often assume.
This supports my choice of Multinomial Naive Bayes on count / TF-IDF features as a fair, well-tuned baseline.

---

## Takeaways for this project

- Bag-of-words / n-gram features with linear models or Naive Bayes are a strong, well-documented baseline.
- The dataset is imbalanced (~13% spam), so report precision, recall, F1 for the spam class (and MCC), not accuracy alone.
- Remove duplicate messages before splitting to avoid leakage between train and test sets.
