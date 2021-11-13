title: Homework 5 BIOSTAT823
date: 11-13-2021
author: Yuxuan Chen

# Homework 4 Dive into Deep Learning

In this homework,I tried to apply neural network technique to classify the insects pictures and used 

## Read Data

```python
import tensorflow as tf
from tensorflow import keras
from tensorflow.keras import layers

import numpy as np

import matplotlib.pyplot as plt
import shap
```

```python
image_size = (300, 300)
batch_size = 30

train_data = tf.keras.preprocessing.image_dataset_from_directory(
    "insects/train",
    labels = "inferred",
    label_mode = "categorical",
    seed=2021,
    image_size=image_size,
    batch_size=batch_size,
)

test_data = tf.keras.preprocessing.image_dataset_from_directory(
    "insects/test",
    labels = "inferred",
    label_mode = "categorical",
    seed=2021,
    image_size=image_size,
    batch_size=batch_size,
)
```




## First glance of the data

```python
for images, labels in train_data.take(1):
    for i in range(9):
        ax = plt.subplot(3, 3, i + 1)
        plt.imshow(images[i].numpy().astype("uint8"))
        plt.axis("off")
```

## Neural Network

```python
# standardize the data
normalization_layer = layers.Rescaling(1./255)

# apply the layer to dataset
normalized_ds = train_data.map(lambda x, y: (normalization_layer(x), y))
image_batch, labels_batch = next(iter(normalized_ds))

# add augmentation to avoid overfitting
data_augmentation = keras.Sequential(
  [
    layers.RandomFlip("horizontal",
                      input_shape=(image_size[0],image_size[1],3)),
    layers.RandomRotation(0.1),
    layers.RandomZoom(0.1),
  ]
)

```

```python
num_classes = 3

model = keras.models.Sequential([
    data_augmentation,
    layers.Rescaling(1./255, input_shape=(image_size[0], image_size[1], 3)),
    layers.Conv2D(16, 3, padding='same', activation='relu'),
    layers.MaxPooling2D(),
    layers.Conv2D(32, 3, padding='same', activation='relu'),
    layers.MaxPooling2D(),
    layers.Conv2D(64, 3, padding='same', activation='relu'),
    layers.MaxPooling2D(),
    layers.Dropout(0.2),
    layers.Flatten(),
    layers.Dense(128, activation='relu'),
    layers.Dense(num_classes)
])
```

```python
model.compile(optimizer='adam',
              loss=tf.keras.losses.CategoricalCrossentropy(from_logits=True),
              metrics=['accuracy'])

```

```python
epochs=20
history = model.fit(
  train_data,
  validation_data=test_data,
  epochs=epochs
)
```

```python
# visualize the results
accuracy = history.history['accuracy']
v_accuracy = history.history['val_accuracy']

loss = history.history['loss']
v_loss = history.history['val_loss']

epochs_range = range(epochs)

plt.figure(figsize=(10, 10))
plt.subplot(1, 2, 1)
plt.plot(epochs_range, accuracy, label='Training Accuracy')
plt.plot(epochs_range, v_accuracy, label='Validation Accuracy')
plt.legend(loc='lower right')
plt.title('Training and Validation Accuracy')

plt.subplot(1, 2, 2)
plt.plot(epochs_range, loss, label='Training Loss')
plt.plot(epochs_range, v_loss, label='Validation Loss')
plt.legend(loc='upper right')
plt.title('Training and Validation Loss')
plt.show()

```


## Shapley Explanation
```python
X_train = np.concatenate([x for x, y in train_data],axis = 0)
y_train = np.concatenate([y for x, y in train_data],axis = 0)

X_test = np.concatenate([x for x, y in test_data],axis = 0)
y_test = np.concatenate([y for x, y in test_data],axis = 0)
```
```python
explainer = shap.GradientExplainer(model, X_train)
sv = explainer.shap_values(X_test[:10])
sv[0].shape
```
```python
model.predict(X_test[:10])
```
```python
shap.image_plot([sv[i] for i in range(3)], X_test[:3])

```
The plot given by shap shows the explanation for each level on the predictions. The red pixels increase the model's output, while the blue pixels decrease the model's output.

















