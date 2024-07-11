---
title: Machine Learning Sentiment Analysis - Evaluate & Save Model
date: 2023-04-18 09:59:07
categories: AI/ML
tags: [AI, NLP, Machine Learning, Tensorflow, Keras, Sentiment Analysis]
---

In previous post, we have trained a machine learning model to classify sentiments of movie reviews. In this post, we will evaluate the performance of the model and save the model for future use.

## Evaluate Model
To evaluate the performance of the model, we will use the test dataset. Using the `evaluate` method of the model to calculate the loss and accuracy of the model on the test dataset.

``` python
    # evaluate model on test dataset
    loss, accuracy = trained_model.evaluate(test_dataset)
    print("Test accuracy:", accuracy)
    print("Test loss:", loss)
```

The `evaluate` method returns two values, the loss and the accuracy of the model on the test dataset. We can use these values to evaluate the performance of the model. below is result of the evaluation on the test dataset:

``` bash
    Test accuracy: 0.8694400191307068
    Test loss: 0.3562678098678589
```

## Save Model
To save the model for future use, we will use the `save` method of the model. The trained model will be saved in the local machine as a `.keras` file. The model file format extension can be `.h5`, `.tf` or `.keras`.

``` python
def save_model(vectorize_layer, model, model_saved_path):
    export_model = tf.keras.Sequential([
        vectorize_layer, # text vectorization layer
        model # the model trained before
    ])

    export_model.compile(
        loss=losses.BinaryCrossentropy(from_logits=False), 
        optimizer="adam", 
        metrics=['accuracy']
    )

    # save model
    export_model.save(model_saved_path)
    return export_model
```

In above code, we first create a new model by combining the `vectorize_layer` and the trained `model`. We then compile the model with the same loss function, optimizer and metrics used during training. Finally, we save the model using the `save` method and return the saved model. 

Here is a thing need to be noticed, we need to put the `vectorize_layer` as the first layer of the model so that it can be used to preprocess the text data before passing it to the trained model.

In the next blog, we will introduce how to load the model and use it to make predictions on new data.