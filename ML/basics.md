
# Machine Learning Fundamentals — Study Notes

## 1. What is Machine Learning?

Machine Learning is the subfield of Computer Science that gives computers the ability to learn patterns from data, **without being explicitly programmed** for every rule.

> **Definition (Arthur Samuel, 1959):** "Field of study that gives computers the ability to learn without being explicitly programmed."

Instead of writing hard-coded rules for every scenario, we feed data to an algorithm and let it discover the underlying patterns/relationships, which it then uses to make predictions or decisions on new, unseen data.

### Why did ML boom in recent years?
Even though ML as a concept dates back to 1959, it only became practical and widespread recently because of:
1. **Advanced processing power** – GPUs/TPUs made it feasible to train large models in reasonable time.
2. **Availability of large amounts of data** – internet, sensors, mobile phones, and digitization generated the massive datasets ML needs to learn effective patterns.

(Other contributing factors interviewers like to hear: better algorithms, cheaper storage, open-source frameworks like TensorFlow/PyTorch, and cloud computing.)

---

## 2. Types of Machine Learning

| Type | Description | Example |
|---|---|---|
| **1. Supervised Learning** | Model learns from **labeled data** (input + correct output) | Spam detection, house price prediction |
| **2. Unsupervised Learning** | Model finds patterns in **unlabeled data** | Customer segmentation, clustering |
| **3. Semi-Supervised Learning** | Uses a **small amount of labeled data** + a **large amount of unlabeled data** | Medical image classification (few labeled scans, many unlabeled ones) |
| **4. Reinforcement Learning** | Agent learns by **interacting with an environment**, receiving rewards/penalties | Game-playing AI, robotics, self-driving cars |

### Where ML fits — the bigger picture
ML is a subfield of **Artificial Intelligence (AI)**, and **Deep Learning (DL)** is, in turn, a subfield of ML that uses multi-layered neural networks to learn from very large datasets.

```
        Artificial Intelligence (AI)
                   |
          Machine Learning (ML)
                   |
            Deep Learning (DL)
```

(This is the classic "concentric circles" overlap diagram — AI is the largest circle, ML sits inside it, and DL sits inside ML.)
<img width="1294" height="1330" alt="image" src="https://github.com/user-attachments/assets/44ac69ce-d60f-48c5-b413-550f77b3a461" />



---

## 3. Model Training, Overfitting & Underfitting

### What is Model Training?
Model training is the process of feeding data to a learning algorithm so it can adjust its internal parameters (weights) to minimize the difference between its predictions and the actual outcomes. The goal is a model that **generalizes well** to new, unseen data — not one that just memorizes the training data.

### What is a Generalized Model?
A generalized model is one that performs consistently well on **both training data and new/unseen (test) data** — it has learned the true underlying pattern rather than memorizing noise or quirks specific to the training set.

### Overfitting
- The model learns the training data **too well**, including noise and outliers.
- Performs great on training data, **poorly on test/unseen data**.
- Indicates **high variance**.

### Underfitting
- The model is **too simple** to capture the underlying pattern in the data.
- Performs poorly on **both** training and test data.
- Indicates **high bias**.

### Bias and Variance — clearing the confusion

This is a commonly misunderstood pair of terms, so let's be precise:

- **Bias** = error due to overly simplistic assumptions in the model. A high-bias model consistently makes the same kind of mistake regardless of which data it sees — it "underfits." Bias is about **how far off, on average, the model's predictions are from the true values** (it's an error metric, not literally "training error").
- **Variance** = error due to the model being overly sensitive to small fluctuations in the training data. A high-variance model performs very differently on different training sets — it "overfits." Variance is about **how much the model's predictions change/vary if trained on a different sample of data**.

**Correcting the informal framing in the original notes:**
- ❌ "Bias is training error and variance is testing error" — not quite accurate.
- ✅ More correct: **High bias → high error on both training AND test data** (underfitting). **High variance → low error on training data BUT high error on test data** (overfitting — the gap between train and test error is the signature of variance).
- ❌ "Variance is the variation in data" — variance is not a property of the raw data; it's a property of **the model's predictions** across different training sets.
- ❌ "Bias means static data" — bias isn't about the data being static; it's about the **model's assumptions being too rigid/simplistic**, causing it to miss real patterns.

### The Bias-Variance Tradeoff
- **Simple models** (e.g., linear regression) → high bias, low variance → underfitting.
- **Complex models** (e.g., deep decision trees) → low bias, high variance → overfitting.
- The goal is to find the **sweet spot** — a model complex enough to capture the true pattern, but not so complex that it memorizes noise. This is visualized classically as a U-shaped total error curve, where:
  - Bias² decreases as complexity increases.
  - Variance increases as complexity increases.
  - Total error = Bias² + Variance + Irreducible error, minimized at some optimal complexity.
 
  - ( add this also bias is training error and variance testing error, can variance should be said it is the variation in data ? and bias mean static data ??)
<img width="320" height="311" alt="image" src="https://github.com/user-attachments/assets/aec98a6d-51f1-4838-9338-d41f4d5f21c5" />


<img width="1133" height="680" alt="image" src="https://github.com/user-attachments/assets/d45982bd-646e-4e89-a318-050c04de8164" />

<img width="512" height="440" alt="image" src="https://github.com/user-attachments/assets/03683670-04c0-4312-b075-405d1961adaf" />



### How to Handle Overfitting and Underfitting

**To fix Underfitting (reduce bias):**
- Use a more complex model (add features, increase model capacity).
- Train longer / reduce regularization strength.
- Add more relevant features or polynomial features.

**To fix Overfitting (reduce variance):**
- Get more training data.
- Use regularization (L1/L2, dropout).
- Simplify the model (fewer features, prune trees, reduce depth).
- Use cross-validation.
- Use ensemble techniques like bagging (e.g., Random Forest) to reduce variance.
- Early stopping during training.

---

## 4. Handling Missing Data

### Why does data go missing?
- Human error (data entry mistakes, skipped fields)
- Sensor/equipment failure
- Data corruption during transfer/storage
- Non-response in surveys
- Data merging issues

### Types of Missing Data

**1. MCAR — Missing Completely At Random**
The missingness has **no relationship** with any variable (observed or unobserved).
*Example:* A lab machine randomly fails to record a blood test result due to a random power glitch — unrelated to the patient or the test value itself.

**2. MAR — Missing At Random**
The missingness is related to **other observed variables**, but not to the missing value itself.
*Example:* Younger patients are less likely to have a bone-density test recorded (missingness depends on age, an observed variable), but not on the bone-density value itself.

**3. MNAR — Missing Not At Random**
The missingness is related to the **value that is missing itself**.
*Example:* People with very high incomes are less likely to disclose their income in a survey — the missingness depends on the (unobserved) income value itself.

### Treatment of Missing Data

| Type | Common Treatment |
|---|---|
| **MCAR** | Safe to use **listwise deletion** (drop rows) or simple imputation (mean/median/mode) — since missingness isn't biased. |
| **MAR** | Use **model-based imputation** (regression imputation, KNN imputation, multiple imputation) using the related observed variables. |
| **MNAR** | Hardest to handle — deletion or naive imputation introduces bias. Requires **domain knowledge**, sensitivity analysis, or explicitly modeling the missingness mechanism (e.g., Heckman correction). |

In statistics and data analysis, **bias** means a **systematic error or skew** that leads to inaccurate conclusions.

Unlike random noise or accidents, bias consistently pushes your results in a specific, wrong direction.

### What Bias Means in Missing Data (MNAR)

When data is **Missing Not at Random (MNAR)**, the missingness itself depends on the unobserved value.

* **The Problem:** People with low incomes might be less likely to report their income on a survey.
* **Naive Imputation / Deletion:** If you delete those missing rows or fill them in with the overall average, you ignore *why* they are missing.
* **The Resulting Bias:** Your calculated average income will be **systematically higher** than the true average because the low-income data was systematically wiped out.

### Summary Comparison

| Concept | What It Represents | Impact on Analysis |
| --- | --- | --- |
| **Variance / Random Error** | Unpredictable noise or natural variation | Increases uncertainty, but averages out over large samples. |
| **Bias / Systematic Error** | Consistent shift away from the true value | Produces confidently wrong conclusions, regardless of sample size. |


**General imputation techniques:**
- Mean/Median/Mode imputation (simple, but reduces variance and can introduce bias if data isn't MCAR)
- KNN imputation
- Regression imputation
- Multiple Imputation by Chained Equations (MICE)
- Using algorithms that handle missing values natively (e.g., XGBoost, LightGBM)
- Creating a "missing indicator" flag column to preserve the information that a value was missing

---

## 5. Real Industry Interview Questions

### Conceptual / Foundational
1. What is Machine Learning? How is it different from traditional programming?
2. What are the different types of Machine Learning? Give a real-world example of each.
3. What's the difference between AI, ML, and Deep Learning?
4. Why did Machine Learning become so popular in the last decade despite being decades old as a concept?
5. What is the difference between parametric and non-parametric models?

### Bias-Variance / Overfitting-Underfitting
6. Explain the bias-variance tradeoff. How would you identify whether your model has high bias or high variance just from the train/test error numbers?
7. Your model has 2% training error and 20% test error. What's happening, and how would you fix it?
8. Your model has 25% training error and 26% test error. What's happening, and how would you fix it?
9. What are some concrete techniques to reduce overfitting in a neural network?
10. How does increasing model complexity affect bias and variance?
11. What's the difference between regularization (L1 vs L2) and how does each affect the bias-variance tradeoff?
12. Explain cross-validation. Why is it useful in detecting overfitting?
13. What is early stopping and how does it help prevent overfitting?

### Missing Data
14. How do you handle missing data in a dataset? Walk me through your process.
15. What's the difference between MCAR, MAR, and MNAR? Why does it matter which type you're dealing with?
16. Is it ever okay to just drop rows with missing values? When would that be a bad idea?
17. What are the risks of mean/median imputation? When would you prefer KNN or model-based imputation instead?
18. How would you detect whether missing data in a column is MNAR versus MCAR?
19. If 40% of a column's values are missing, would you impute or drop the column? What factors would influence your decision?

### Applied / Scenario-Based
20. You're given a dataset with high-cardinality categorical missing values. How do you handle it?
21. You built a model that performs very well in training but poorly in production. Walk me through how you'd debug this.
22. How would you explain the bias-variance tradeoff to a non-technical stakeholder?
23. What metrics would you use to detect if a model is overfitting, before even looking at test data?
24. Suppose your dataset has missing values only in the target/label column — how do you approach that differently than missing values in feature columns?
25. Give an example from your own project/experience where you diagnosed and fixed an overfitting problem.

---

### Quick Interview Cheat-Sheet

| Concept | One-liner to remember |
|---|---|
| Bias | Error from wrong assumptions → underfitting |
| Variance | Error from sensitivity to training data → overfitting |
| MCAR | Missing for no reason at all |
| MAR | Missing depends on *other* observed data |
| MNAR | Missing depends on the *missing value itself* |
| Fix underfitting | More complexity, more features, less regularization |
| Fix overfitting | More data, regularization, simpler model, ensembling |
