---
title: Machine Learning Sentiment Analysis - Load Raw Dataset
date: 2023-02-14 09:42:45
categories: AI/ML
tags: [AI, NLP, Machine Learning, Tensorflow, Keras, Sentiment Analysis]
---

Recently, the `ChatGPT` is very hot in the NLP community. It is a transformer-based language model that can generate human-like conversations. It attracts me to learn about Machine Learning to build a AI model. In this blog, I will talk about how to build a Sentiment Analysis model, the topic will be separated into several blogs, including Load raw dataset,Text Vectorization, Model Training, and Model Evaluation, Model Testing.

## Sentiment Analysis
Sentiment Analysis is a natural language processing (NLP) task that involves classifying the sentiment of a given text into one of the predefined categories such as positive, negative, or neutral. The goal of sentiment analysis is to understand the attitude or opinion of the writer towards a particular topic or product.

{% image /assets/images/ai-ml/sentiment-analysis.png, width=600px, alt=Sentiment Analysis %}

## Dataset
To build and train a sentiment analysis model, we need a dataset. The dataset used in this blog is the `IMDB movie review dataset`. You can find this dataset more information in previous blogs. It contains 50,000 movie reviews labeled as `positive` or `negative`. The dataset is split into 25,000 reviews for training and 25,000 reviews for testing.

After downloading the dataset, unzip the file and put it in a directory named `dataset`. The directory structure should look like this:

```
dataset
├── test
│   ├── neg
│   └── pos
└── train
    ├── neg
    └── pos
```

## Loading the Raw Data
We will load the raw data using `tensorflow` and `keras` libraries. The `text_dataset_from_directory` function is used to load the dataset. The function takes the directory path, batch size, validation split, subset, and seed as input parameters. 

The `validation_split` parameter is used to split the dataset into training and validation sets. For example, if the `validation_split` is set to 0.2, 20% of the data will be used for validation.

The `subset` parameter is used to specify which subset of the data to load. `training` value is used to load only the training data. `validation` value is used to load only the validation data. The `seed` parameter is used to ensure reproducibility.

``` python
import tensorflow as tf
import config


def load_raw_train_dataset():
    return tf.keras.utils.text_dataset_from_directory(
        config.ds_train_dir,
        batch_size=config.ds_batch_size, # batch size
        validation_split=config.validate_split, # 10% of data will be used for validation
        subset='training', # only training data will be used
        seed=config.ds_batch_seed) # to ensure reproducibility


def load_raw_validation_dataset():
    return tf.keras.utils.text_dataset_from_directory(
        config.ds_train_dir,
        batch_size=config.ds_batch_size,
        validation_split=config.validate_split,
        subset='validation',
        seed=config.ds_batch_seed)


def load_raw_test_dataset():
    return tf.keras.utils.text_dataset_from_directory(
        config.ds_test_dir,
        batch_size=config.ds_batch_size)
```

In above code, there are three functions defined to load the raw data. The `load_raw_train_dataset` function is used to load the training data, the `load_raw_validation_dataset` function is used to load the validation data, and the `load_raw_test_dataset` function is used to load the test data.

For the `config.py` file, we will define the following parameters for above raw data loading functions.

``` python
# dataset directory for training
ds_train_dir = "dataset/train"

# dataset directory for testing
ds_test_dir = "dataset/test"

# batch size for training and testing
ds_batch_size = 32

# random seed for shuffling the data
ds_batch_seed = 36  

# 10% of data will be used for validation
validate_split = 0.1
```

In above code, we are loading the raw data from the `dataset/train` and `dataset/test` directories. The `batch_size` parameter is set to 32, which means that the data will be loaded in batches of 32. The `validation_split` parameter is set to 0.1, which means that 10% of the data will be used for validation. The `subset` parameter is set to `training` to load only the training data, `validation` to load only the validation data. The `seed` parameter is set to 36 to ensure reproducibility.

So for now, we have loaded the raw data, in the next blog, we will talk about text vectorization which is the process of converting text into numerical form that can be used for training and testing the model.
