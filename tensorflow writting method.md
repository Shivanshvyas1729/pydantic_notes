>  PART 1

# TensorFlow/Keras Layer Writing Methods

---

# 1. Functional API Style (Most Common)

```python
from tensorflow.keras import layers

inputs = layers.Input(shape=(224, 224, 3))

x = layers.Conv2D(21, 3, padding='same')(inputs)

x = layers.ReLU()(x)
```

## Explanation

```text
layers.Conv2D(...)   → creates layer object
(inputs)             → applies layer on input tensor
```

## Best For

* Complex architectures
* ResNet
* EfficientNet
* Multiple inputs/outputs
* Skip connections

---

# 2. Separated Layer Object Style

```python
from tensorflow.keras import layers

inputs = layers.Input(shape=(224, 224, 3))

conv = layers.Conv2D(21, 3, padding='same')

x = conv(inputs)

relu = layers.ReLU()

x = relu(x)
```

## Explanation

```text
First create layer
Then apply layer separately
```

## Best For

* Beginners
* Debugging
* Understanding internals

---

# 3. Sequential API Style

```python
import tensorflow as tf
from tensorflow.keras import layers

model = tf.keras.Sequential([

    layers.Conv2D(
        21,
        3,
        padding='same',
        input_shape=(224, 224, 3)
    ),

    layers.ReLU(),

    layers.MaxPooling2D(2)

])
```

## Explanation

```text
Sequential automatically passes output
from one layer to next layer
```

No manual:

```python
x = layer(x)
```

needed.

## Best For

* Simple CNNs
* Linear architectures
* Quick prototyping

---

# 4. Custom Layer Style

```python
import tensorflow as tf
from tensorflow.keras import layers

class MyBlock(layers.Layer):

    def __init__(self):

        super().__init__()

        self.conv = layers.Conv2D(
            32,
            3,
            padding='same'
        )

        self.relu = layers.ReLU()

    def call(self, inputs):

        x = self.conv(inputs)

        x = self.relu(x)

        return x
```

## Explanation

```text
Layers stored inside self
then used inside call()
```

## Best For

* Reusable CNN blocks
* MobileNet
* Residual blocks
* Attention modules
* Custom architectures

---

# 5. Direct Activation Inside Conv Layer

```python
from tensorflow.keras import layers

x = layers.Conv2D(
    32,
    3,
    activation='relu',
    padding='same'
)(inputs)
```

## Explanation

```text
Conv + ReLU combined together
```

---

# Expanded Version

```python
x = layers.Conv2D(32, 3, padding='same')(inputs)

x = layers.ReLU()(x)
```

Both are equivalent.

---

# Modern Best Practice

Usually preferred:

```python
x = layers.Conv2D(32, 3, padding='same')(inputs)

x = layers.BatchNormalization()(x)

x = layers.ReLU()(x)
```

instead of:

```python
x = layers.Conv2D(
    32,
    3,
    activation='relu'
)(inputs)
```

because:

```text
BatchNorm works better before ReLU
```

---

# Important Syntax Understanding

This:

```python
layers.Conv2D(32, 3)(inputs)
```

means:

```python
conv = layers.Conv2D(32, 3)

x = conv(inputs)
```

---

# Kernel Size Example

```python
layers.Conv2D(32, 3)
```

means:

3 \times 3

kernel/filter size.

---

# Quick Comparison Table

| Method            | Best For           | Flexible | Easy   |
| ----------------- | ------------------ | -------- | ------ |
| Functional API    | Complex models     | ✅        | Medium |
| Separated Object  | Learning/debugging | ✅        | ✅      |
| Sequential        | Simple models      | ❌        | ✅✅     |
| Custom Layer      | Reusable blocks    | ✅✅       | Medium |
| activation='relu' | Quick coding       | Medium   | ✅      |

**# 1. Activation Functions

---
- - 
> part 2

## Method 1 — Separate Layer

```python
x = layers.Dense(128)(x)

x = layers.ReLU()(x)
```

---

## Method 2 — Inside Layer

```python
x = layers.Dense(
    128,
    activation='relu'
)(x)
```

---

# 2. Input Layer

---

## Method 1 — Explicit Input

```python
inputs = layers.Input(shape=(224,224,3))
```

Used in Functional API.

---

## Method 2 — input_shape in First Layer

```python
layers.Conv2D(
    32,
    3,
    input_shape=(224,224,3)
)
```

Used in Sequential API.

---

# 3. Model Creation

---

## Sequential API

```python
model = tf.keras.Sequential([
    layers.Dense(64),
    layers.Dense(10)
])
```

Simple linear flow.

---

## Functional API

```python
inputs = layers.Input(shape=(100,))

x = layers.Dense(64)(inputs)

outputs = layers.Dense(10)(x)

model = Model(inputs, outputs)
```

Flexible architecture.

---

# 4. Pooling Layers

---

## MaxPooling

```python
layers.MaxPooling2D(2)
```

Takes maximum value.

---

## AveragePooling

```python
layers.AveragePooling2D(2)
```

Takes average value.

---

## Global Average Pooling

```python
layers.GlobalAveragePooling2D()
```

Converts feature map into vector.

Very common in modern CNNs.

---

# 5. Concatenation

---

## Functional Style

```python
x = layers.concatenate([x1, x2])
```

---

## Layer Object Style

```python
concat = layers.Concatenate()

x = concat([x1, x2])
```

---

# 6. Adding Tensors

Used in ResNet skip connections.

---

## Functional Style

```python
x = layers.add([x1, x2])
```

---

## Layer Object Style

```python
adder = layers.Add()

x = adder([x1, x2])
```

---

# 7. Flattening

---

## Flatten Layer

```python
x = layers.Flatten()(x)
```

Converts:

```text
7×7×512 → 25088
```

---

## Global Average Pooling

```python
x = layers.GlobalAveragePooling2D()(x)
```

Converts:

```text
7×7×512 → 512
```

Modern preferred approach.

---

# 8. Dropout

---

## Standard

```python
x = layers.Dropout(0.5)(x)
```

---

## Training Condition

```python
x = layers.Dropout(0.5)(x, training=True)
```

Sometimes used in advanced models.

---

# 9. Batch Normalization

---

## Standard

```python
x = layers.BatchNormalization()(x)
```

---

## With Parameters

```python
x = layers.BatchNormalization(
    momentum=0.9,
    epsilon=1e-5
)(x)
```

---

# 10. Convolution Variants

---

## Standard Conv

```python
layers.Conv2D(64, 3)
```

---

## Depthwise Conv

```python
layers.DepthwiseConv2D(3)
```

---

## Separable Conv

```python
layers.SeparableConv2D(64, 3)
```

TensorFlow already combines:

```text
Depthwise + Pointwise
```

internally.

---

## Transposed Conv

```python
layers.Conv2DTranspose(64, 3)
```

Used in:

* GANs
* U-Net
* super resolution

---

# 11. Activation Alternatives

---

## ReLU

```python
layers.ReLU()
```

---

## LeakyReLU

```python
layers.LeakyReLU(0.1)
```

---

## GELU

```python
layers.Activation('gelu')
```

Used in Transformers.

---

## Swish

```python
layers.Activation('swish')
```

Used in EfficientNet.

---

# 12. Custom Layers

---

## Lambda Layer

```python
x = layers.Lambda(
    lambda x: x / 255.0
)(x)
```

Quick custom operation.

---

## Full Custom Layer

```python
class MyLayer(layers.Layer):

    def call(self, inputs):

        return inputs * 2
```

---

# 13. Model Compilation

---

## String Style

```python
model.compile(
    optimizer='adam',
    loss='binary_crossentropy'
)
```

---

## Object Style

```python
model.compile(
    optimizer=tf.keras.optimizers.Adam(0.001),
    loss=tf.keras.losses.BinaryCrossentropy()
)
```

More configurable.

---

# 14. Loss Functions

---

## String

```python
loss='categorical_crossentropy'
```

---

## Object

```python
loss=tf.keras.losses.CategoricalCrossentropy()
```

---

# 15. Optimizers

---

## Simple

```python
optimizer='adam'
```

---

## Detailed

```python
optimizer=tf.keras.optimizers.Adam(
    learning_rate=0.001
)
```

---

# 16. TensorFlow vs tf.nn

---

## Keras Layer Style

```python
layers.ReLU()(x)
```

---

## TensorFlow Low-Level Style

```python
tf.nn.relu(x)
```

Both common.

---

# 17. Reshaping

---

## Reshape Layer

```python
x = layers.Reshape((7,7,64))(x)
```

---

## tf.reshape

```python
x = tf.reshape(x, (-1,7,7,64))
```

---

# 18. Normalization

---

## BatchNorm

```python
layers.BatchNormalization()
```

---

## LayerNorm

```python
layers.LayerNormalization()
```

Mostly Transformers.

---

# 19. Model Saving

---

## Entire Model

```python
model.save("model.h5")
```

---

## Weights Only

```python
model.save_weights("weights.h5")
```

---

# 20. Training

---

## Simple

```python
model.fit(x_train, y_train)
```

---

## Using Dataset API

```python
model.fit(train_dataset)
```

Production-level approach.

---

# Most Important Ones to Master First

Focus on deeply understanding:

```text
Sequential vs Functional API
activation='relu' vs ReLU()
Conv2D vs DepthwiseConv2D
Flatten vs GlobalAveragePooling
tf.nn.relu vs layers.ReLU
String vs Object optimizers/losses
```

These appear everywhere in modern deep learning.
