<img width="2816" height="1536" alt="image" src="https://github.com/user-attachments/assets/750a4ca6-6146-4a0e-aa19-ad02337be068" />[paper](https://drive.google.com/file/d/1Tv7Q6qpaJCSE72zsyXi4TOpOH6MNKXyL/view?usp=sharing)
# Fibonacci-Net
<img width="1200" height="436" alt="image" src="https://github.com/user-attachments/assets/196d2309-20db-49e4-a03c-b77db598643c" />

<img width="2816" height="1536" alt="image" src="https://github.com/user-attachments/assets/627fe188-aade-498c-a57e-6079478b29cf" />



Fibonacci-Net is a lightweight CNN architecture designed for:
- efficient feature extraction
- low computational cost
- mobile/edge AI deployment
- medical imaging
- small datasets

It combines:
- Fibonacci-based filter scaling
- depthwise separable convolutions
- residual/skip connections
- Avg-2Max pooling

---

# Architecture Diagram

[Excalidraw Architecture Link](https://link.excalidraw.com/p/readonly/nTbHqs6Z6NhUWvvaQ1Zq)

---

# 1. Fibonacci Convolution Arrangement
(Fibonacci-based filters)

## Idea

Instead of increasing filters aggressively like:

```text
32 → 64 → 128
```

Fibonacci-Net increases them using Fibonacci numbers:

```text
21 → 34 → 55 → 89
```

The Fibonacci sequence:

```text
1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89...
```

---

<u>## What are filters?</u>

Filters (kernels) in CNNs detect:
- edges
- textures
- shapes
- patterns

More filters = more learning capacity.

---

## Why use Fibonacci growth?

Traditional CNNs often:
- consume large memory
- use many parameters
- overfit on small datasets

Fibonacci scaling provides smoother growth.

### Benefits

- fewer parameters
- lower memory usage
- lightweight architecture
- gradual feature learning

---

## Intuition

Instead of sudden complexity jumps:

```text
32 → 64 → 128
```

we use smoother growth:

```text
21 → 34 → 55 → 89
```

This improves efficiency and regularization.

---

# 2. Depthwise Separable Convolution

A lightweight alternative to standard convolution.

---

## Standard Convolution

Normal CNN convolution processes all channels together.

Example:
RGB image → one filter operates on R, G, and B simultaneously.

This is computationally expensive.

---d<u></u>

## Depthwise Separable Convolution

It splits convolution into TWO operations.

---

## Step 1: Depthwise Convolution

Each channel is processed independently.

```text
R → filter
G → filter
B → filter
```

No channel mixing yet.

---

## Step 2: Pointwise Convolution

A `1 × 1` convolution combines channel information.

This performs feature fusion.

---

## Computational Efficiency

### Standard convolution cost

```math
K × K × M × N
```

### Depthwise separable convolution cost

```math
K × K × M + M × N
```

This drastically reduces computation.

---

## Benefits

- faster training
- reduced computation
- fewer parameters
- ideal for mobile and embedded AI

Used in:
- MobileNet
- Xception

---

# 3. Residual / Skip Connections

Inspired by ResNet.

---

## Main Idea

Outputs from earlier layers are passed directly to deeper layers.

```text
Layer1 → Layer2 → Layer3 → Layer4
   \_________________________/
```

Layer1 information skips intermediate layers.

---

## Why is this important?

Deep networks suffer from:
- vanishing gradients
- feature degradation
- information loss

Skip connections help gradients flow easily.

---

## Residual Learning

Instead of learning:

```math
H(x)
```

the network learns:

```math
F(x) = H(x) - x
```

Final output:

```math
y = F(x) + x
```

This simplifies optimization.

---

## Benefits

### Prevents vanishing gradients
Enables stable deep learning.

### Preserves important features
Earlier representations survive deeper layers.

### Enables deeper CNNs
Allows training of very deep architectures.

---

## Intuition

If deeper layers fail to improve features,
the original features are still preserved.

---

# 4. Avg-2-Max Pooling

Combines:
- Max Pooling
- Average Pooling

into a hybrid pooling strategy.

---

# Max Pooling

Selects the strongest activation.

Example:

```math
\begin{bmatrix}
1 & 5 \\
2 & 8
\end{bmatrix}
\rightarrow 8
```

Good for:
- edge detection
- dominant features

But may lose smooth information.

---

# Average Pooling

Computes the average activation.

```math
\frac{1+5+2+8}{4}=4
```

Good for:
- smooth textures
- global context

But weak features may dominate.

---

# Avg-2-Max Pooling Idea   (used in skip connection)

```text
Output = Average Information + Max Information
```

This captures:
- sharp local features
- smooth contextual features

--->  Give  a good read 
<mark>1. It Creates "Overlapping Pooling" to Prevent Information LossStandard Pooling (Pool 2, Stride 2): The pooling window moves exactly 2 pixels at a time with no overlap. It aggressively discards 75% of your feature data in one step.Overlapping Pooling (Pool 3, Stride 2): The 3×3 window moves 2 steps at a time. This means adjacent pooling regions overlap by 1 row/column of pixels. This redundancy ensures that features on the boundaries of the pooling window are not abruptly cut off or entirely lost, allowing the network to retain a richer context.

2. Digital Image Processing (DIP) AnalogyAccording to the Fibonacci-Net research paper, this custom layer relies heavily on traditional image processing principles:An Average Pooling layer with a 3×3 pool size acts exactly like a traditional blur/smoothing filter.By subtracting twice the Max Pooling output (avg - 2 * max), a 3×3 pool size forces the layer to behave like a sharp edge detection filter.A 3×3 spatial window is the absolute minimum requirement to extract structural edges in all directions (horizontal, vertical, and diagonal). A 2×2 pool size cannot capture geometric edges accurately.

3. Automatic Feature Augmentation & InversionThe math in your custom call function (avg_pool - 2 * max_pool) creates a mathematical "negative" inversion of the feature maps.A 3×3 window provides enough local pixel variance to compute a stable average versus maximum contrast.This inversion forces the network to give extreme attention to structural boundaries. For Fibonacci-Net's primary task (identifying things like brain tumors on MRI scans), a 3×3 grid size is perfect for spotlighting the abnormal perimeter of a lesion or tumor.
> 
## Benefits

### Better feature extraction
Captures diverse representations.

### Improved image understanding
Useful for medical and texture-based datasets.

### Better edge-texture balance
Retains both strong and subtle information.

---

# Overall Flow of Fibonacci-Net

## Step-by-Step Pipeline

### 1. Fibonacci Filter Arrangement
Efficient feature scaling.

↓

### 2. Depthwise Separable Convolution
Fast lightweight computation.

↓

### 3. Residual Connections
Preserve information across layers.

↓

### 4. Avg-2-Max Pooling
Capture both sharp and smooth features.

---

# Handling Class Imbalance

Fibonacci-Net is NOT explicitly designed for class imbalance,
but several architectural components naturally help minority-class learning.

---

# Components Helping Class Imbalance

## 1. Skip Connections / Parallel Concatenation (Strongest Effect)

- reuse shallow + deep features
- improve gradient flow
- preserve rare-class information
- create richer feature diversity

This prevents minority-class features from being forgotten.

---

## 2. Avg-2Max Pooling

Combines:
- Max pooling → strong edge features
- Average pooling → smooth/global features

Helps preserve:
- subtle textures
- weak edges
- rare patterns

Useful for minority classes containing fine-grained details.

---

## 3. Fibonacci Filter Scaling

Gradual scaling:
- reduces parameters
- lowers overfitting
- improves generalization on small datasets

Indirectly reduces bias toward majority classes.

---

## 4. Depthwise Separable Convolution

Main contribution:
- lightweight computation
- efficiency
- smaller models

Indirect imbalance benefit:
- smaller networks tend to overfit less

But it is NOT a direct imbalance-learning mechanism.

---

# Overall Imbalance Assessment

Fibonacci-Net helps imbalance through:
- richer feature diversity
- better feature preservation
- improved gradient propagation
- reduced overfitting

However, it is NOT sufficient for severe long-tail imbalance problems.

---

# Recommended Additions for Strong Imbalance

For highly imbalanced datasets, combine Fibonacci-Net with:
- Focal Loss
- Class-Weighted Cross Entropy
- Oversampling / Data Augmentation
- Balanced Batch Sampling

Use metrics like:
- F1-score
- Recall
- ROC-AUC

instead of accuracy alone.

---

# Why Fibonacci-Net is Useful

Designed for:
- lightweight AI
- edge devices
- medical imaging
- mobile deployment
- small datasets

Goals:
- reduce computation
- reduce model size
- maintain high accuracy
- improve efficiency
- reduce overfitting

---

# One-Line Summary

> Fibonacci-Net combines Fibonacci-based filter scaling, lightweight convolutions, residual learning, and hybrid pooling to build an efficient and compact CNN architecture.
