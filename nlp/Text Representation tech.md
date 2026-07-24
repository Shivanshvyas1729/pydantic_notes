Here are your notes, cleaned up and structured for quick reading, interview prep, and easy revision.

## 1. Why Text Representation?

Machine Learning algorithms cannot directly understand text — they require numerical vectors. The process of converting text into numerical form is called **Text Representation** or **Feature Extraction from Text**.

---

## 2. One Hot Encoding (OHE)

**Concept:** Each unique word in the vocabulary is represented as a binary vector. Only one index is marked as 1 (the word’s position in the vocabulary); the rest are 0.

**Working Example:**
Sentences: "I love NLP", "I love Python", "Python is great"
Vocabulary: `["I", "love", "NLP", "Python", "is", "great"]`

| Word | One Hot Vector |
| --- | --- |
| I | [1, 0, 0, 0, 0, 0] |
| love | [0, 1, 0, 0, 0, 0] |
| NLP | [0, 0, 1, 0, 0, 0] |
| Python | [0, 0, 0, 1, 0, 0] |
| is | [0, 0, 0, 0, 1, 0] |
| great | [0, 0, 0, 0, 0, 1] |

**Problems with OHE:**

* **Sparse Representation:** Vectors are mostly zeros, leading to massive memory waste.
* **Curse of Dimensionality:** A vocabulary of 10,000 words results in a vector length of 10,000 for every single word.
* **Variable Input Length:** Different sentence lengths create irregularly sized inputs, which makes training ML models difficult.
* **Out of Vocabulary (OOV):** Any new or unseen word cannot be represented.
* **No Semantic Meaning:** Words with similar meanings (like "walk" and "run") have completely unrelated vectors.

> **Interview Tip:** One Hot Encoding is easy to implement but impractical for large NLP tasks due to high sparsity and a complete lack of semantic understanding.

---

## 3. Bag of Words (BoW)

**Concept:** Instead of a simple 0/1 presence, we count how often each word appears in a document. This focuses entirely on word occurrence and frequency, ignoring grammar and word order.

**Working Example:**
Doc1: "This is a good movie"
Doc2: "This is not a good movie"
Vocabulary: `[this, is, a, good, movie, not]`

| Word | Doc1 Count | Doc2 Count |
| --- | --- | --- |
| this | 1 | 1 |
| is | 1 | 1 |
| a | 1 | 1 |
| good | 1 | 1 |
| movie | 1 | 1 |
| not | 0 | 1 |

*Doc1 vector:* `[1, 1, 1, 1, 1, 0]`
*Doc2 vector:* `[1, 1, 1, 1, 1, 1]`

**Advantages:**

* Creates fixed-size vectors for entire documents.
* Simple to understand and computationally fast.
* Handles OOV relatively well by simply ignoring missing words.
* Provides a basic idea of word importance based on frequency.

**Disadvantages:**

* Completely ignores word order, which destroys context. (Notice how adding "not" changes the entire meaning of Doc2, yet its vector is nearly identical to Doc1).
* Becomes highly sparse and dimensional for large vocabularies.
* Fails to capture underlying semantic meaning.

> **Interview Tip:** BoW works well for simple document classification (like spam detection) but fails in tasks requiring context or sentiment understanding.

---

## 4. Bag of N-Grams

**Concept:** An N-Gram is a sequence of *N* consecutive words. Instead of evaluating single words, we analyze groups of words to retain local context.

**Working Example:**
Sentence: "This is not a good movie"

* **Unigrams (1-word):** [this], [is], [not], [a], [good], [movie]
* **Bigrams (2-word):** [this is], [is not], [not a], [a good], [good movie]
* **Trigrams (3-word):** [this is not], [is not a], [not a good], [a good movie]

By applying the BoW frequency model to bigrams, the model can now differentiate between phrases like "is not" and "is a".

**Advantages:**

* Captures local word order and short-term context.
* Differentiates similar sentences with opposite meanings (e.g., catching "not good").
* Extends the BoW model in a straightforward way.

**Disadvantages:**

* Dimensionality explodes exponentially. For a 10,000-word vocabulary, there are theoretically 100,000,000 possible bigram combinations.
* Still fails to capture deep semantic meaning or long-distance dependencies within a paragraph.

> **Interview Tip:** Bag of N-Grams improves over BoW by capturing short-term context, but it comes at the steep cost of exponentially increasing your feature space.

---

## 5. Comparison Table

| Feature | One Hot Encoding | Bag of Words | Bag of N-Grams |
| --- | --- | --- | --- |
| **Representation Type** | Binary Vector | Frequency Vector | Sequence Frequency Vector |
| **Captures Order?** | No | No | Partial (Local context) |
| **Captures Meaning?** | No | No | No |
| **Handles OOV Words?** | No | Partial | Partial |
| **Dimensionality** | Very High | High | Extremely High |
| **Best Use Case** | Simple categorization | Document classification | Sentiment analysis, phrase tasks |

---

## 6. Code Implementation (Python)

```python
from sklearn.feature_extraction.text import CountVectorizer

# Sample documents
docs = ["This is a good movie", "This is not a good movie"]

# 1. Bag of Words (Unigrams by default)
vectorizer_bow = CountVectorizer()
X_bow = vectorizer_bow.fit_transform(docs)

print("BoW Vocabulary:", vectorizer_bow.get_feature_names_out())
print("BoW Vectors:\n", X_bow.toarray())

# 2. Bag of Bigrams (N-Grams)
vectorizer_ngrams = CountVectorizer(ngram_range=(2,2))
X_ngrams = vectorizer_ngrams.fit_transform(docs)

print("\nBigram Vocabulary:", vectorizer_ngrams.get_feature_names_out())
print("Bigram Vectors:\n", X_ngrams.toarray())

```

---

## 7. Interview-Level Q&A

**Q1: What is the primary difference between One Hot Encoding and Bag of Words?**
OHE uses a binary 0/1 indicator merely to show if a word exists in the vocabulary, generating a vector per word. BoW counts the frequency of words, aggregating them to represent an entire document in a single vector.

**Q2: What specific problem does Bag of N-Grams solve?**
It captures local word order and short contexts (like negations: "not good"), which BoW completely ignores.

**Q3: What are the main issues with using One Hot Encoding in NLP?**
It creates highly sparse matrices, suffers from the curse of dimensionality, fails to capture any semantic meaning between similar words, and breaks when encountering Out of Vocabulary (OOV) words.

**Q4: What is "sparsity," and why is it a problem in machine learning?**
Sparsity occurs when a vector or matrix is populated mostly by zeros. This wastes vast amounts of memory and slows down computation without adding meaningful information to the model.

**Q5: What modern techniques replace BoW and N-Grams?**
The industry moved to TF-IDF for better frequency weighting, and then to dense embeddings like Word2Vec, GloVe, FastText, and eventually Transformer models like BERT, which capture deep semantic meaning and bidirectional context efficiently.

---

## 8. Summary & Evolution

| Stage | Core Representation | Key Drawback |
| --- | --- | --- |
| **One Hot Encoding** | Binary presence of words | Highly sparse, no meaning captured |
| **Bag of Words** | Word frequency count | Total loss of word order and context |
| **Bag of N-Grams** | Word sequence counts | Dimensionality explodes rapidly |

**The Evolution Pipeline:**
OHE ➡️ Bag of Words ➡️ N-Grams ➡️ TF-IDF ➡️ Word2Vec ➡️ BERT



Here are your notes on TF-IDF and Custom Features, structured for quick revision, clear understanding, and interview preparation.

## 1. Why Do We Need TF-IDF?

Previous techniques (One Hot Encoding, Bag of Words, Bag of N-Grams) treat **every word equally**. They give the same weight to common words ("is", "the", "people") as they do to highly specific, meaningful words ("campus", "selection").

**The Goal:** We need a method that gives **more weight** to meaningful/rare words and **less weight** to generic/common words.

---

## 2. What is TF-IDF?

**TF-IDF** stands for **Term Frequency–Inverse Document Frequency**. It is a statistical measure used to evaluate how important a word is to a specific document relative to an entire corpus (collection of documents).

**The Intuition:** A word is highly important if:

1. It appears **many times** in a specific document (High TF).
2. It appears **rarely** across all other documents (High IDF).

---

## 3. How It Works: Step-by-Step

### Step 1: Term Frequency (TF)

Measures how frequently a word occurs in a single document.

* **Range:** 0 to 1 (can be viewed as the probability of that word in the document).
* **Example:** In the document *"People watch campus"*, the TF for "campus" is $1/3 \approx 0.33$.

### Step 2: Inverse Document Frequency (IDF)

Measures how rare a word is across *all* documents in the corpus.

* If a word is in **every** document $\rightarrow$ IDF is **0** (not important).
* If a word is in **few** documents $\rightarrow$ IDF is **high** (important).

### Step 3: Combine TF and IDF

Multiply the two values to get the final weight of the word for that document:


$$TF\text{-}IDF(t,d) = TF(t,d) \times IDF(t)$$

### Example Calculation

Assume we have 4 documents total, and we are evaluating Document 1: *"People watch campus"*.

| Word | TF (Doc 1) | Doc Frequency (DF) | IDF = log(N/DF) | Final TF-IDF |
| --- | --- | --- | --- | --- |
| **people** | 1/3 | 2 | log(4/2) = 0.30 | **0.10** |
| **watch** | 1/3 | 1 | log(4/1) = 0.60 | **0.20** |
| **campus** | 1/3 | 3 | log(4/3) = 0.12 | **0.04** |

*(Notice how "watch" gets the highest score because it is the most unique to this specific document).*

> **Interview Tip:** Why do we use "log" in the IDF formula?
> Without the log, extremely rare words would get disproportionately massive values. The logarithm normalizes the scale, preventing any single rare word from dominating the model and maintaining a balance between TF and IDF.

---

## 4. Python Implementation (Scikit-Learn)

```python
from sklearn.feature_extraction.text import TfidfVectorizer

docs = [
    "People watch campus selection",
    "Campus placement drive",
    "Write comments on campus",
    "Watch people comment online"
]

# Create TF-IDF object
vectorizer = TfidfVectorizer()

# Fit and transform the documents
X = vectorizer.fit_transform(docs)

# Outputs
print("Vocabulary:", vectorizer.get_feature_names_out())
print("IDF Values:", vectorizer.idf_)
print("TF-IDF Matrix:\n", X.toarray())

```

*Note: Scikit-learn uses a smoothed formula by default: $log((N + 1) / (df + 1)) + 1$ to prevent division by zero.*

---

## 5. Pros, Cons, and Applications

**Advantages:**

* **Weights Important Words:** Unique, discriminative terms get higher scores.
* **Search Engine Friendly:** Excellent for ranking documents by relevance.
* **Lightweight:** Computationally fast and highly interpretable.

**Disadvantages:**

* **Sparsity:** Still produces high-dimensional, sparse matrices.
* **No Semantics:** Cannot understand context (treats "good" and "great" as totally unrelated).
* **Static Meaning:** A word's representation doesn't adapt to the context of the sentence.
* **OOV Problem:** Ignores words not present in the training vocabulary.

**Real-World Applications:**

* Search Engines (Information Retrieval)
* Text Classification (Spam detection, Sentiment analysis)
* Keyword Extraction

---

## 6. Custom (Hand-Crafted) Features

Sometimes automated features like TF-IDF aren't enough. You can engineer **domain-specific custom features** and combine them with TF-IDF to create **Hybrid Features** for better model performance.

**Examples for Sentiment Analysis:**

| Feature | Description |
| --- | --- |
| **Positive/Negative Word Count** | Counting words like "excellent" vs. "terrible" |
| **Pos/Neg Ratio** | Total positive words / Total negative words |
| **Review Length** | Word or character count of the text |
| **Punctuation Markers** | Number of exclamation marks (indicates emotional intensity) |
| **Formatting Quirks** | Presence of emojis or ALL CAPS ("LOVED IT!! 😍") |

---

## 7. Interview Q&A Cheatsheet

**Q1: What is TF-IDF?**
A statistical measure that evaluates how important a word is to a specific document within a larger corpus by weighing term frequency against inverse document frequency.

**Q2: What is the range of TF and IDF values?**
TF ranges from 0 to 1. IDF is $\ge$ 0 (the rarer the term, the higher the IDF).

**Q3: What are the shared limitations of classical NLP techniques (OHE, BoW, TF-IDF)?**
They all rely on sparse representations, fail to capture contextual or semantic meaning (synonyms are treated as entirely different features), and struggle with huge vocabularies.

---

## 8. Summary of Classical NLP Techniques

| Technique | Core Concept | Biggest Strength | Biggest Weakness |
| --- | --- | --- | --- |
| **One-Hot** | Binary word presence | Simple to understand | No meaning, massive sparsity |
| **Bag of Words** | Word frequency | Captures word occurrence | Destroys word order/context |
| **N-Grams** | Word sequence counts | Captures local context | Dimensionality explodes |
| **TF-IDF** | Word importance | Highlights unique terms | Sparse, no semantic understanding |
| **Word2Vec/BERT** | Deep Embeddings | Captures deep meaning/context | Requires massive data/compute |

*(This pipeline shows exactly why the industry evolved from basic counting methods to advanced semantic embeddings like Word2Vec and BERT).*
