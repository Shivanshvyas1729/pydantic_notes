Here are some structured notes based on the paper you are viewing:

<details><summary>LSTM</summary>
# Notes: Time Series & Long Short-term Memory (LSTM) RNN

## 1. Introduction

* **Time Series:** A sequence of historical measurements recorded at equal time intervals. Common patterns include:
* **Trend:** Long-term increase or decrease.
* **Seasonality:** Affected seasonally at known/fixed frequencies.
* **Cyclical:** Rises/falls at unfixed frequencies (e.g., economic cycles).


* **Evolution of Models:** Traditional structured statistical models (like ARIMA) are increasingly challenged by Machine Learning models like Recurrent Neural Networks (RNNs) and LSTMs due to more complex data and computing power.
* **Training LSTMs:** LSTMs are predominantly supervised learning architectures. They are trained by initializing random weights, feeding training data, measuring errors, and adjusting weights using **back-propagation**.

## 2. Foundations & Architecture

* **Recurrent Neural Networks (RNNs):** Unlike standard neural networks, RNNs recur a hidden variable $h_t$ (short-term memory) that passes information from previous iterations to the current one.
* **The Vanishing Gradient Problem:** As standard RNNs process longer sequences, the gradients (used to update weights during training) tend to vanish or explode, making it hard to learn long-term dependencies.
* **The LSTM Solution:** LSTMs solve the vanishing gradient problem by introducing an internal **cell state** $c_t$ (long-term memory) alongside the hidden state $h_t$. The cell state acts like a conveyor belt, carefully regulated by three gates:
* **Forget Gate:** Decides what information to erase from the cell state using a sigmoid activation function (outputs values between 0 and 1).
* **Input Gate:** Controls what new, relevant information is saved to the cell state (using a combination of $tanh$ and sigmoid functions).
* **Output Gate:** Combines the adjusted cell state $c_t$ with the current input and previous output to predict the next hidden state $h_t$.



## 3. Current Applications

LSTMs excel in handling temporal and sequential data across various domains:

* **Solar Power Production Forecasting:**
* Used to predict power generation using weather data as input.
* **Advantage:** LSTMs outperformed physical reference models (achieving lower RMSE and MAE) because they are highly flexible, less affected by measurement errors, and can learn complex cross-series dependencies (trend, seasonality, and level).


* **Natural Language Processing (NLP):**
* Used for sequence modeling, such as language modeling and generating word embeddings (e.g., ELMo).
* **Advantage:** **Bidirectional LSTMs** (processing text both forwards and backwards) allow models to learn context-sensitive embeddings. For example, understanding that "bank" means something different in "river bank" versus "Bank of America".



## 4. Alternative Statistical Methods

While LSTMs are powerful, traditional statistical methods remain relevant depending on the data:

* **ARIMA (Autoregressive Integrated Moving Average):** A general class of linear forecasting models.
* **AR (Autoregressive):** Predicts future variables using a linear combination of past values.
* **I (Integrated):** Uses differencing to reduce trend and seasonality, transforming a non-stationary time series into a stationary one (constant mean and statistical properties over time).
* **MA (Moving Average):** Predicts future values based on past forecast errors.

</details>
<details><summary>LSTM imp point</summary>
  Based on the [Time Series Long Short-term Memory RNN paper](https://arxiv.org/pdf/2105.06756) you are viewing, here are the most critical, high-impact concepts to master. These points frequently come up in Machine Learning and Data Science interviews and will help you demonstrate a deep understanding of sequential modeling.

### 1. The "Why": The Vanishing Gradient Problem (Very High Interview Frequency)

Interviewers almost always ask *why* we use LSTMs instead of standard Recurrent Neural Networks (RNNs).

* **The Problem:** In standard RNNs, as you back-propagate the error through many time steps (the "time" dimension), the gradients (the values used to update weights) tend to shrink toward zero (vanish) or grow uncontrollably (explode).
* **The Impact:** Because of vanishing gradients, standard RNNs physically cannot learn or remember **long-term dependencies** in sequence data.

### 2. The "How": LSTM Architecture & Gates (The Core Interview Topic)

You must be able to explain how an LSTM solves the vanishing gradient problem. LSTMs introduce an internal **cell state** ($c_t$) which acts like a conveyor belt for long-term memory, bypassing the rapid decay of standard hidden states ($h_t$). The flow of information onto this conveyor belt is carefully controlled by **three gates**:

* **Forget Gate:** Decides what information to throw away from the previous cell state. It uses a sigmoid activation function to output a value between 0 (forget entirely) and 1 (keep entirely).
* **Input Gate:** Controls what *new* information is relevant and should be added to the cell state (using a combination of $tanh$ and sigmoid functions).
* **Output Gate:** Looks at the newly updated cell state, current input, and previous output to decide what the next short-term hidden state ($h_t$) should be.

### 3. Time Series Fundamentals

You should be able to define the underlying characteristics of the data you are modeling.

* **Trend:** A long-term increase or decrease in the data.
* **Seasonality:** Patterns that repeat at known, fixed frequencies (e.g., higher sales every December).
* **Cyclicality:** Rises and falls at unfixed frequencies (e.g., unpredictable economic booms and busts).

### 4. Model Comparison: LSTM vs. ARIMA (Applied Knowledge)

Interviewers love to test if you know *when* to use Deep Learning versus traditional statistical methods.

* **ARIMA (Autoregressive Integrated Moving Average):** A structured, linear model. It requires the data to be made **stationary** (having a constant mean and variance over time), usually through differencing (the "I" or Integrated part of ARIMA). It struggles with highly complex, non-linear data.
* **LSTMs:** Highly flexible, unstructured models. They do not require the data to be perfectly stationary in the same way ARIMA does, and they excel at learning complex cross-series dependencies (e.g., looking at multiple weather features at once to predict solar power). They are also generally more robust to measurement errors.

### 5. Advanced Context: Bidirectional LSTMs

If the interview shifts toward Natural Language Processing (NLP), mention Bidirectional LSTMs.

* **What they do:** They process the sequence both forwards and backwards.
* **Why it matters:** It allows the model to learn context-sensitive representations. For example, looking forward and backward in a sentence allows the model to realize that the word "bank" means something entirely different in "river bank" compared to "Bank of America".

---

Would you like to dive deeper into the mathematical formulas behind one of the specific LSTM gates, or focus more on how to explain the traditional ARIMA model?
</details>
