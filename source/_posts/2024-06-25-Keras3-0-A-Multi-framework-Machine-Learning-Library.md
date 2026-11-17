---
title: Keras3.0 - A Multi-framework Machine Learning Library
date: 2024-06-25 10:12:36
categories: AI
tags: [AI, Machine Learning, Keras, TensorFlow, PyTorch, JAX]
---

`Keras`3 is a full rewrite of Keras that enables you to run your Keras workflows on top of either `JAX`, `TensorFlow`, or `PyTorch`, and that unlocks brand new large-scale model training and deployment capabilities. It's multi-framework machine learning, meaning that you can use Keras to train models on top of different backends, and deploy them to different platforms. You can also use Keras as a low-level cross-framework language to develop custom components such as layers, models, or metrics that can be used in native workflows in JAX, TensorFlow, or PyTorch — with one codebase.

{% image /assets/images/ai-ml/keras3-multi-framework-machine-learning.jpg, alt=Keras 3 Multi-framework Machine Learning %}

## What's New in Keras 3?
Keras 3 introduces several exciting features that enhance its usability, performance, and flexibility:

#### Unified API
Keras 3 continues to build on its legacy of a user-friendly and intuitive API. It aims to unify the high-level and low-level APIs more seamlessly, providing a consistent experience across different backends such as TensorFlow, PyTorch, and others.

#### Multi-backend Support
While Keras has traditionally been closely associated with TensorFlow, Keras 3 expands its compatibility to other popular deep learning frameworks. This means you can now use Keras with PyTorch and other backends, leveraging Keras' high-level abstractions and ease of use across different environments.

#### Improved Performance
Efforts have been made in Keras 3 to optimize performance across various operations, ensuring faster execution times and better utilization of hardware resources. This improvement is crucial for handling larger datasets and complex models efficiently.

#### Enhanced Model Deployment
Keras 3 simplifies the process of deploying trained models to production environments. With streamlined APIs for model serialization and deployment tools, it becomes easier to integrate Keras models into real-world applications.

#### Expanded Model Zoo
Keras 3 comes with an expanded model zoo, offering `pre-trained models` for a wider range of tasks and domains. This includes vision models (e.g., ResNet, EfficientNet), NLP models (e.g., BERT, GPT), and other specialized architectures, all accessible through a unified interface.

#### Advanced AutoML Capabilities
The new release includes improved AutoML capabilities, allowing developers to automate model selection, hyperparameter tuning, and architecture search. This feature can significantly accelerate the model development process, especially for beginners and researchers exploring new domains.

## Pre-trained Models
There's a wide range of pretrained models that you can start using today with Keras 3. About 40 Keras Applications models (the `keras.applications` namespace) are available in all backends. These models are pre-trained on large datasets and can be used for transfer learning or fine-tuning. It includes:

#### Pre-trained Models for Natural Language Processing
- Albert
- Bart
- Bert
- Bloom
- DebertaV3
- DistilBert
- Gemma
- Electra
- Falcon
- FNet
- GPT2
- Llama
- Llama3
- Mistral
- OPT
- PaliGemma
- Phi3
- Roberta
- XLMRoberta

#### Pre-trained Models for Computer Vision
- CSPDarkNet
- EfficientNetV2
- MiT
- MobileNetV3
- ResNetV1
- ResNetV2
- VideoSwinB
- VideoSwinS
- VideoSwinT
- VitDet
- YOLOV8
- ImageClassifier
- VideoClassifier
- CLIP
- RetinaNet


## How to Get Started with Keras 3?

#### 1.Install Keras 3
Ensure you have the latest version of Keras installed. You can install Keras via pip if you haven't already:

``` shell
pip install --upgrade keras
```

#### 2.Define Model
Use Keras' high-level API to define your deep learning model. Here’s a simple example of a convolutional neural network (CNN) for image classification:

``` python
from keras.layers import Conv2D, MaxPooling2D, Flatten, Dense
from keras.models import Sequential

model = Sequential([
    Conv2D(32, (3, 3), activation='relu', input_shape=(32, 32, 3)),
    MaxPooling2D((2, 2)),
    Conv2D(64, (3, 3), activation='relu'),
    MaxPooling2D((2, 2)),
    Flatten(),
    Dense(64, activation='relu'),
    Dense(10, activation='softmax')
])
```

#### 3.Compile and Train Model
Compile the model with a loss function, optimizer, and metrics, then train it on your data:

``` python
model.compile(optimizer='adam',
              loss='sparse_categorical_crossentropy',
              metrics=['accuracy'])

model.fit(train_images, train_labels, epochs=10, batch_size=32, validation_data=(val_images, val_labels))
```

#### 4.Deploy models
Keras 3 provides a simple and unified interface for deploying trained models to production environments. You can serialize your models and deploy them using tools such as TensorFlow Serving, PyTorch Hub, or JAX Hub. 


## Summary
Keras 3 bring a lot of exciting features to the table, including multi-backend support, improved performance, and enhanced model deployment. It also includes a wide range of pre-trained models for natural language processing and computer vision, making it easy to get started with deep learning. With these features, Keras 3 is a powerful and flexible tool for building and deploying deep learning models.


