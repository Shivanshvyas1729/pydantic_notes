
RAG (Retrieval-Augmented Generation) evaluation is much broader than normal ML evaluation because **two different systems must be evaluated independently**:
# RAG Evaluation

## 1. Retriever Evaluation
- Precision@K
- Recall@K
- MRR (Mean Reciprocal Rank)
- MAP (Mean Average Precision)
- Hit Rate
- nDCG (Normalized Discounted Cumulative Gain)

## 2. Generator Evaluation
- BLEU
- ROUGE
- BERTScore
- Exact Match
- Semantic Similarity
- METEOR

## 3. End-to-End Evaluation
- Latency
- Throughput
- Cost
- User Satisfaction
- Success Rate
- Error Rate

---

# RAG-Specific Metrics

- Faithfulness
- Context Precision
- Context Recall
- Context Relevancy
- Answer Relevancy
- Answer Correctness
- Context Utilization
- Hallucination Rate
- Citation Accuracy
- Noise Sensitivity

---

# Evaluation Methodologies

- Offline Evaluation
- Online Evaluation
- Human-in-the-Loop
- LLM-as-a-Judge
- Pairwise Evaluation
- A/B Testing
- Continuous Evaluation

---

# Component-Level Evaluation

- Chunking
- Embeddings
- Retriever
- Re-ranker
- Prompt
- LLM

---

# Production Monitoring

- Observability
- Robustness
- Safety
- Cost Analysis















<details><summary> basics </summary>

- 
1. **Retriever** (Did it retrieve the correct documents?)
2. **Generator / LLM** (Did it generate the correct answer?)
3. **Entire RAG Pipeline** (Did the user actually get a useful answer?)

This is why RAG has **three categories of metrics**.

---

# 1. Retriever Evaluation Metrics

These evaluate whether the vector database/search engine retrieved the correct chunks.

```
Query
   │
   ▼
Vector Search
   │
Retrieved Chunks
   │
Evaluation
```

---

# A. Precision@K

Measures:

> Out of the top K retrieved documents, how many are actually relevant?

Formula

[
Precision@K=\frac{Relevant\ Retrieved}{K}
]

Example

Suppose

```
Retrieved Top 5

1 Relevant
2 Relevant
3 Irrelevant
4 Relevant
5 Irrelevant
```

Relevant = 3

K = 5

Precision@5

```
3 / 5 = 0.6
```

Higher is better.

---

### Python

```python
def precision_at_k(retrieved, relevant):
    retrieved_k = retrieved[:len(relevant)]

    relevant_found = len(
        set(retrieved_k) &
        set(relevant)
    )

    return relevant_found / len(retrieved_k)
```

---

# B. Recall@K

Measures

> Did we retrieve ALL relevant documents?

Formula

[
Recall@K=
\frac{Relevant\ Retrieved}
{Total\ Relevant}
]

Example

There are

```
Total relevant documents = 10
```

Retrieved

```
7 relevant
```

Recall

```
7 / 10 = 0.7
```

---

### Python

```python
def recall_at_k(retrieved, relevant):
    relevant_found = len(
        set(retrieved) &
        set(relevant)
    )

    return relevant_found / len(relevant)
```

---

# C. Hit Rate

Simplest metric.

Question

> Did we retrieve at least ONE correct document?

Formula

```
Hit = 1
Miss = 0
```

Example

Top 10 retrieval

```
Correct document exists

Hit = 1
```

Otherwise

```
0
```

---

Python

```python
def hit_rate(retrieved, relevant):
    return int(
        len(set(retrieved) & set(relevant)) > 0
    )
```

---

# D. Mean Reciprocal Rank (MRR)

Evaluates

> How early was the first correct document found?

Formula

[
MRR=\frac{1}{Rank}
]

Example

Correct document appears

```
Rank 1

MRR =1
```

Rank 2

```
1/2=0.5
```

Rank 5

```
1/5=0.2
```

Higher is better.

---

Python

```python
def reciprocal_rank(retrieved, relevant):

    for i, doc in enumerate(retrieved):

        if doc in relevant:
            return 1 / (i + 1)

    return 0
```

---

# E. MAP (Mean Average Precision)

Average Precision over many queries.

Suppose

```
Query1 AP =0.80

Query2 AP =0.60

Query3 AP =0.90
```

MAP

```
(0.80+0.60+0.90)/3
```

Very common in information retrieval.

---

# F. nDCG

Normalized Discounted Cumulative Gain

Measures

* ranking quality
* position importance

Correct documents appearing earlier receive higher scores.

Widely used by Google search and recommendation systems.

---

Python (using sklearn)

```python
from sklearn.metrics import ndcg_score

score = ndcg_score(
    y_true,
    y_score
)
```

---

# 2. Generation Metrics

These evaluate the LLM answer.

---

# A. Exact Match (EM)

Answer must exactly equal ground truth.

Example

Ground truth

```
Paris
```

Prediction

```
Paris
```

Score

```
1
```

Prediction

```
City of Paris
```

Score

```
0
```

Very strict.

---

Python

```python
def exact_match(pred, truth):
    return pred.strip() == truth.strip()
```

---

# B. BLEU

Used mainly in translation.

Measures

Word overlap.

Example

Truth

```
The cat sat on mat
```

Prediction

```
The cat sat on the mat
```

BLEU is high.

---

Python

```python
from nltk.translate.bleu_score import sentence_bleu

score = sentence_bleu(
    [reference],
    candidate
)
```

---

# C. ROUGE

Measures overlap

Especially useful for summarization.

Types

```
ROUGE-1

ROUGE-2

ROUGE-L
```

Python

```python
from rouge_score import rouge_scorer

scorer = rouge_scorer.RougeScorer(
    ['rouge1', 'rougeL'],
    use_stemmer=True
)

scores = scorer.score(
    reference,
    prediction
)
```

---

# D. METEOR

Improves BLEU.

Considers

* synonyms
* stemming
* word order

Python

```python
from nltk.translate.meteor_score import meteor_score

score = meteor_score(
    [reference],
    prediction
)
```

---

# E. BERTScore

Instead of word matching

Uses BERT embeddings.

Meaning

```
Dog chased cat

Puppy ran after kitten
```

High similarity.

Python

```python
from bert_score import score

P, R, F1 = score(
    predictions,
    references,
    lang="en"
)
```

---

# F. Semantic Similarity

Uses embedding cosine similarity.

Python

```python
from sentence_transformers import SentenceTransformer
from sklearn.metrics.pairwise import cosine_similarity

model = SentenceTransformer(
    "all-MiniLM-L6-v2"
)

emb1 = model.encode([prediction])
emb2 = model.encode([reference])

score = cosine_similarity(
    emb1,
    emb2
)
```

---

# 3. RAG-Specific Metrics

These became popular with frameworks like **RAGAS**, **DeepEval**, **TruLens**, and **ARES** because traditional NLP metrics (BLEU, ROUGE, etc.) don't tell you whether the model used the retrieved context correctly.

---

# A. Context Precision

Question

> Were the retrieved documents actually useful?

High

```
Retriever fetched only useful chunks.
```

Low

```
Retriever fetched lots of irrelevant chunks.
```

---

# B. Context Recall

Question

> Did retrieval include everything needed?

If important information is missing

↓

Low recall.

---

# C. Faithfulness

Most important RAG metric.

Question

> Is the answer completely supported by the retrieved documents?

Example

Context

```
Einstein was born in Germany.
```

Answer

```
Einstein was born in Germany.
```

Faithful ✔

Answer

```
Einstein was born in France.
```

Hallucination ✘

---

Python (RAGAS)

```python
from ragas.metrics import faithfulness
```

---

# D. Answer Relevancy

Question

> Did the answer actually answer the user's question?

Question

```
What is Python?
```

Answer

```
Python is a programming language.
```

Relevant ✔

Answer

```
Python was created by Guido.
```

Partially relevant.

---

Python

```python
from ragas.metrics import answer_relevancy
```

---

# E. Context Relevancy

Question

> Are the retrieved chunks relevant to the question?

Useful for detecting retrieval noise.

---

# F. Context Utilization

Measures

How much of retrieved context was actually used.

Retriever may fetch

```
10 chunks
```

LLM uses only

```
2 chunks
```

Poor utilization.

---

# G. Answer Correctness

Measures

Ground truth vs generated answer.

Combination of

* semantic similarity
* factual correctness

Available in RAGAS.

---

# H. Noise Sensitivity

Measures

How robust is the model when irrelevant documents are mixed into the retrieved context.

Good RAG systems ignore noise.

---

# I. Hallucination Rate

Measures

Percentage of generated facts **not supported** by the retrieved context.

Example

```
100 generated facts

12 unsupported

Hallucination Rate = 12%
```

Lower is better.

---

# J. Citation Accuracy

Measures

Whether the generated citations or referenced documents actually support the claims.

Common in enterprise and legal RAG systems.

---

# 4. End-to-End System Metrics

These evaluate the overall user experience.

| Metric            | Meaning                                    |
| ----------------- | ------------------------------------------ |
| Latency           | Time taken to answer                       |
| Retrieval Time    | Vector search speed                        |
| Generation Time   | LLM response time                          |
| Throughput        | Queries processed per second               |
| Cost per Query    | Token + embedding + retrieval cost         |
| User Satisfaction | Human ratings or feedback                  |
| Success Rate      | Percentage of tasks completed successfully |
| Error Rate        | Failed or invalid responses                |

---

# 5. Frameworks for RAG Evaluation

## 1. RAGAS (Most Popular)

Provides metrics such as:

* Faithfulness
* Answer Relevancy
* Context Precision
* Context Recall
* Answer Correctness
* Context Utilization

Example:

```python
from ragas import evaluate
from ragas.metrics import (
    faithfulness,
    answer_relevancy,
    context_precision,
    context_recall,
)

result = evaluate(
    dataset,
    metrics=[
        faithfulness,
        answer_relevancy,
        context_precision,
        context_recall,
    ],
)

print(result)
```

---

## 2. DeepEval

Useful for:

* Hallucination detection
* Answer relevancy
* Faithfulness
* Bias
* Toxicity
* Prompt evaluation

Example:

```python
from deepeval.metrics import FaithfulnessMetric

metric = FaithfulnessMetric()

score = metric.measure(
    test_case
)

print(score)
```

---

## 3. TruLens

Focuses on:

* Groundedness
* Context relevance
* Answer relevance
* End-to-end pipeline tracing

Example:

```python
from trulens_eval import Tru

tru = Tru()

# Instrument your RAG application
# and evaluate groundedness,
# context relevance, and answer relevance.
```

---

## 4. ARES

Designed for automated RAG evaluation using learned evaluators, reducing dependence on human annotation.

---

# Summary Table

| Category     | Metric              | What it Measures                                           |
| ------------ | ------------------- | ---------------------------------------------------------- |
| Retrieval    | Precision@K         | Fraction of retrieved documents that are relevant          |
| Retrieval    | Recall@K            | Fraction of all relevant documents retrieved               |
| Retrieval    | Hit Rate            | Whether at least one relevant document was retrieved       |
| Retrieval    | MRR                 | Rank position of the first relevant document               |
| Retrieval    | MAP                 | Average precision across multiple queries                  |
| Retrieval    | nDCG                | Ranking quality with emphasis on higher-ranked documents   |
| Generation   | Exact Match         | Exact equality with the reference answer                   |
| Generation   | BLEU                | N-gram overlap (common in translation)                     |
| Generation   | ROUGE               | Overlap for summaries and long-form text                   |
| Generation   | METEOR              | Lexical similarity with stemming and synonyms              |
| Generation   | BERTScore           | Semantic similarity using contextual embeddings            |
| Generation   | Semantic Similarity | Embedding-based cosine similarity                          |
| RAG-Specific | Context Precision   | Relevance of retrieved context                             |
| RAG-Specific | Context Recall      | Completeness of retrieved context                          |
| RAG-Specific | Faithfulness        | Whether the answer is fully supported by retrieved context |
| RAG-Specific | Answer Relevancy    | How well the answer addresses the user's question          |
| RAG-Specific | Context Relevancy   | Alignment between retrieved chunks and the query           |
| RAG-Specific | Context Utilization | Degree to which retrieved context is actually used         |
| RAG-Specific | Answer Correctness  | Factual and semantic correctness of the answer             |
| RAG-Specific | Noise Sensitivity   | Robustness to irrelevant retrieved documents               |
| RAG-Specific | Hallucination Rate  | Percentage of unsupported generated claims                 |
| RAG-Specific | Citation Accuracy   | Correctness of citations or supporting references          |
| End-to-End   | Latency             | Total response time                                        |
| End-to-End   | Retrieval Time      | Time spent retrieving documents                            |
| End-to-End   | Generation Time     | Time spent generating the response                         |
| End-to-End   | Throughput          | Queries handled per unit time                              |
| End-to-End   | Cost per Query      | Computational and API cost of each request                 |
| End-to-End   | User Satisfaction   | Human evaluation of usefulness                             |
| End-to-End   | Success Rate        | Percentage of tasks completed successfully                 |
| End-to-End   | Error Rate          | Frequency of failed or invalid responses                   |

### Which metrics matter most in practice?

If you're building a production RAG system, these are the metrics that are most commonly monitored:

* **Retriever quality:** Precision@K, Recall@K, MRR, nDCG
* **Answer quality:** Faithfulness, Answer Relevancy, Answer Correctness
* **Hallucination control:** Faithfulness, Hallucination Rate, Citation Accuracy

  Yes. **Human-in-the-Loop (HITL)** is one of the most important evaluation and improvement mechanisms for RAG systems, especially in production. However, **it is not an evaluation metric itself**. Instead, it is an **evaluation methodology (or feedback loop)** where humans review, rate, or correct the system's outputs.

---

# What is Human-in-the-Loop (HITL)?

Human-in-the-Loop means that **a human participates in evaluating or improving the RAG system instead of relying only on automated metrics**.

```text
User Query
      │
      ▼
Retriever
      │
Retrieved Documents
      │
      ▼
LLM Generates Answer
      │
      ▼
Human Reviews Answer
      │
      ├── Correct ✔
      ├── Incorrect ✘
      ├── Hallucination
      ├── Missing Information
      ├── Poor Retrieval
      ▼
Feedback Stored
      │
Improve Retriever / Prompt / LLM
```

---

# Why do we need HITL?

Automated metrics like:

* Faithfulness
* BLEU
* ROUGE
* BERTScore
* Context Precision

cannot perfectly judge whether an answer is truly useful or acceptable.

For example:

**Question**

> "Can diabetics eat mangoes?"

Generated answer:

> "Yes, but only in moderate amounts and after consulting a doctor."

A human doctor may rate this as **Excellent**.

BLEU or ROUGE may give a poor score because the wording differs from a reference answer.

---

# What do humans evaluate?

Humans usually score several aspects of a RAG response.

| Criterion        | What is evaluated?                                 |
| ---------------- | -------------------------------------------------- |
| Correctness      | Is the answer factually correct?                   |
| Faithfulness     | Is every claim supported by the retrieved context? |
| Relevance        | Does the answer address the user's question?       |
| Completeness     | Is any important information missing?              |
| Clarity          | Is the answer easy to understand?                  |
| Helpfulness      | Would this answer satisfy the user?                |
| Hallucination    | Did the model invent unsupported facts?            |
| Citation Quality | Are the cited documents actually relevant?         |

---

# Example

### Question

```text
What is the capital of France?
```

Retrieved document

```text
France's capital is Paris.
```

Generated answer

```text
Paris is the capital of France.
```

Human evaluation

```text
Correctness      ⭐⭐⭐⭐⭐
Faithfulness     ⭐⭐⭐⭐⭐
Clarity          ⭐⭐⭐⭐⭐
Hallucination    None
```

---

Another example:

Question

```text
Who invented Python?
```

Retrieved document

```text
Python is a programming language.
```

Generated answer

```text
Python was invented by Elon Musk.
```

Human evaluation

```text
Correctness      ⭐☆☆☆☆
Faithfulness     ☆☆☆☆☆
Hallucination    Severe
```

The human can immediately identify an error that automated metrics may miss if they don't have the appropriate reference or retrieval context.

---

# Rating scales

Most organizations use simple rating scales.

### 1–5 Star Rating

```text
5 = Excellent

4 = Good

3 = Average

2 = Poor

1 = Bad
```

---

### Binary Rating

```text
Correct

Incorrect
```

---

### Yes / No

```text
Faithful?

Yes

No
```

---

### Pass / Fail

```text
Useful

Not Useful
```

---

# How is the feedback used?

Human feedback can improve several parts of a RAG system.

```text
Human Feedback
      │
      ▼
Store Ratings
      │
      ├── Improve Embeddings
      ├── Improve Chunking
      ├── Improve Retriever
      ├── Improve Prompt
      ├── Fine-tune LLM
      ├── Build Better Evaluation Dataset
      ▼
Better RAG System
```

---

# Example feedback record

```json
{
  "question": "Who invented Python?",
  "retrieved_context": "Python is a programming language.",
  "generated_answer": "Python was invented by Elon Musk.",
  "correctness": 1,
  "faithfulness": 1,
  "relevance": 2,
  "hallucination": true,
  "comments": "Answer contradicts the retrieved context."
}
```

---

# Human-in-the-Loop tools

Several evaluation frameworks support incorporating human judgments:

* **RAGAS** – Human-labeled datasets can be used to validate automated metrics.
* **DeepEval** – Supports manual review alongside automated evaluation.
* **TruLens** – Allows developers to inspect traces and collect human feedback.
* **LangSmith** – Supports annotators reviewing runs, adding scores, and comparing prompts or model versions.
* **Phoenix (Arize)** – Provides observability and human annotation workflows for LLM applications.

---

# HITL vs Automated Metrics

| Feature                        | Automated Metrics | Human-in-the-Loop |
| ------------------------------ | ----------------- | ----------------- |
| Speed                          | Very fast         | Slow              |
| Cost                           | Low               | High              |
| Scalability                    | Excellent         | Limited           |
| Detects hallucinations         | Sometimes         | Very well         |
| Judges usefulness              | Limited           | Excellent         |
| Understands context and nuance | Limited           | Excellent         |
| Requires reference answers     | Often             | Not always        |

---

# When should you use HITL?

Human-in-the-Loop is especially valuable for:

* Medical RAG systems
* Legal document assistants
* Financial advisory assistants
* Enterprise knowledge bases
* Customer support bots
* Any high-stakes application where incorrect answers could have significant consequences

In these domains, it's common to combine **automated metrics** (such as Faithfulness, Context Precision, and Answer Relevancy) with **periodic human review** to ensure quality while keeping evaluation scalable.

* **Production performance:** Latency, Throughput, Cost per Query

These provide a balanced view of retrieval accuracy, generation quality, factual reliability, and operational efficiency.
</details>
