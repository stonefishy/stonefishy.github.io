---
title: A Large Movie Review Dataset for Binary Sementic Analysis
date: 2023-01-11 16:14:32
categories: AI/ML
tags: [AI, Machine Learning, Keras, TensorFlow, Dataset]
---

## Introduction
In this article, we will discuss about a large movie review dataset for `binary semantic analysis`. It is extracted from IMDb(https://www.imdb.com/) movie sites and handled by `Stanford` university. The dataset contains 50,000 movie reviews labeled as positive or negative. It is divided into training and testing sets with 25,000 and 25,000 movie reviews respectively. It is labeled as positive or negative based on the sentiment of the review. It also include an additional 50,000 unlabeled documents for unsupervised learning. 

## Dataset Information
#### Positive Reviews
The positive reviews contains 25000 movie reviews labeled as positive. A positive review has a score >= 7 out of 10. The positive reviews are mostly positive and entertaining. The reviews are mostly written in the positive tone. In the `aclImdb\train\pos` directory, the suffix number of files all `>= 7`. Example file name like `10_8.txt` which means star rating `8/10` from IMDb.

#### Negative Reviews
The negative reviews contains 25000 movie reviews labeled as negative. A negative review has a score `<= 4` out of 10. The negative reviews are mostly negative and sad. The reviews are mostly written in the negative tone. In the `aclImdb\train\neg` directory, the suffix number of files all `<= 4`. Example file name like `10_3.txt` which means star rating `3/10` from IMDb.

The reviews with more neutral ratings are not included in the train/test sets.

#### Unlabeled Reviews
The unlabeled reviews contains 50000 movie reviews that are not labeled as positive or negative. These reviews are used for unsupervised learning. In the `aclImdb\train\unsup` directory, these reviews are not labeled as positive or negative, the suffix number of files all are `0` which means the score. Example file name like `10_0.txt`.

#### Test dataset reviews
The test reviews contains 25000 movie reviews. These reviews are used for evaluating the performance of the model. In the `aclImdb\test` directory, it contains `pos` and `neg` directories with the same structure as the `train` directory.

#### Tokenized BoW
In addition to the review text files, there is a already-tokenized bag of words (BoW) features that were used in `Standford` experiments. These are stored in .feat files in the train/test directories. Each .feat file is in LIBSVM format, an ascii sparse-vector format for labeled data. The feature indices in these files start from 0, and the text tokens corresponding to a feature index is found in [imdb.vocab]. So a line with 0:7 in a .feat file means the first word in [imdb.vocab](the) appears 7 times in that review.

## Download the Dataset
The complete dataset can be downloaded from the following link: https://ai.stanford.edu/~amaas/data/sentiment/aclImdb_v1.tar.gz. Once you download and unzip it, you can see the major dataset structure as shown below:

```
aclImdb
├── README
├── imdb.vocab 
├── test
│   ├── neg
│   │   ├── 2_1.txt
│   └── pos
│   │   ├── 3_8.txt
├── train
│   ├── neg
│   │   ├── 12_1.txt
│   ├── pos
│   │   ├── 4_8.txt
│   └── unsup
│   │   ├── 10_0.txt
```
In the `.txt` file, the text content is review of the movie.



