---
title: Machine Learning Sentiment Analysis - Compile Model
date: 2023-03-23 15:26:05
categories: AI/ML
tags: [AI, NLP, Machine Learning, Tensorflow, Keras, Sentiment Analysis]
---

In previous blog, we have built a machine learning model to classify the sentiment of a given text. In this blog, we will introduce how to compile the model.

## Compile Model
After building the model, we need to compile it. Compiling the model involves specifying the loss function, optimizer, and evaluation metric.

### Loss Function
The loss function is used to measure the difference between the predicted output and the actual output. It is used to minimize the difference between the predicted output and the actual output.

There are several loss functions available in `Keras`:

- Binary Crossentropy
- Categorical Crossentropy

`损失函数（Loss Function）`是机器学习和深度学习中用于衡量模型预测值与真实值之间差异的函数。它的作用是评估模型的预测性能，并指导模型在训练过程中进行参数调整，以最小化预测误差。

- **度量误差**：损失函数计算模型预测值与真实值之间的差异，从而度量模型的预测误差。
- **优化目标**：损失函数为模型的优化提供了一个明确的目标。在训练过程中，优化算法（如梯度下降）会调整模型的参数，以最小化损失函数的值。
- **评估性能**：损失函数的值可以用于评估模型的性能。通常，损失函数的值越小，模型的预测性能越好

### Optimizer
The optimizer is used to update the weights of the model during training. It adjusts the weights to minimize the loss function. There are several optimizers available in `Keras`:

- SGD (Stochastic Gradient Descent)
- Adam (Adaptive Moment Estimation)
- Adagrad
- Adadelta
- RMSprop

`优化器（Optimizer）`是机器学习和深度学习中用于更新模型参数以最小化损失函数的算法。它的作用是通过调整模型的参数，使得损失函数的值尽可能小，从而提高模型的预测性能。

- **参数更新**：优化器根据损失函数的梯度信息，计算出模型参数的更新方向和步长，从而更新模型的参数。
- **加速收敛**：优化器通过各种策略（如动量、自适应学习率等），加速模型的收敛过程，使得模型更快地达到最优解。
- **防止过拟合**：优化器可以通过正则化等技术，防止模型在训练数据上过拟合，提高模型的泛化能力。

### Evaluation Metric
The evaluation metric is used to measure the performance of the model.  There are several evaluation metrics available in `Keras`:

- Accuracy
- Precision
- Recall
- F1 Score
- AUC (Area Under the Curve)

`指标（Metrics）`是机器学习和深度学习中用于评估模型性能的量化标准。它们的作用是提供一种客观的方式来衡量模型的预测能力，并帮助用户了解模型在不同方面的表现。

- **性能评估**：指标用于评估模型在训练、验证和测试数据上的性能。不同的指标可以衡量模型在不同方面的表现，如准确率、精确率、召回率、F1分数等。
- **监控训练过程**：指标可以用于监控模型的训练过程，帮助用户了解模型是否在正确地学习和改进。例如，通过观察训练和验证指标的变化，可以判断模型是否过拟合或欠拟合。
- **模型选择**：指标可以用于比较不同模型的性能，从而选择最优的模型。例如，在多个模型中，可以选择在验证数据上表现最好的模型进行进一步的测试和部署。

Now, let's compile our model with `Binary Crossentropy` loss function, `Adam` optimizer, and `Accuracy` evaluation metric. Below is code.

```python
  # compile the model
  model.compile(
     # Loss function (二分类交叉熵损失函数), aim to calculate the difference between the predicted and actual values
      loss=losses.BinaryCrossentropy(), 
      # A adam optimizer(自适应矩估计优化器)， a popular optimizer for neural networks, aim to minimize the loss function
      optimizer='adam', 
      # Binary Accuracy metric(二分类准确率评估指标)，>0.5 is positive，<0.5 is negative
      metrics=[tf.metrics.BinaryAccuracy(threshold=0.5)]
    ) 
```

In above code, we have used `BinaryCrossentropy` loss function, `adam` optimizer, and `BinaryAccuracy` metric. We have also set the threshold to 0.5, which means that if the predicted value is greater than 0.5, it is considered as positive, otherwise it is considered as negative. 

Now, we have compiled our model. In next blog, we will introduce the process of training the model.