---
title: Machine Learning Sentiment Analysis - Load Model & Predict
date: 2023-05-02 15:29:58
categories: AI/ML
tags: [AI, NLP, Machine Learning, Tensorflow, Keras, Sentiment Analysis, AWS]
---

In previous blog, we have saved the trained model in the local. In this post, we will load the saved model and predict the sentiment of a given text.

## Load Model
To load the saved model, we will use the `load_model` function from the tensorflow keras library. It's very simple to load the saved model. We just need to pass the path of the saved model as an argument to the `load_model` function.

```python
def load_model(model_path):
    model = tf.keras.models.load_model(model_path)
    return model
```

In above code, we have defined a function `load_model` which takes the path of the saved model as an argument. We then load the saved model using the `load_model` function and return the loaded model.  Next step we will use this model object to predict the sentiment of a given text.

## Predict Sentiment
To predict the sentiment of a given text, we will use the `predict` function of the loaded model. It's very simple to predict the sentiment of a given text using the loaded model. We defined below function to predict the sentiment of a given text. It can accepts a single text or a list of texts.

```python
def predict_sentiment(model_path, sentence):
    # load the model from disk
    loaded_model = train_model.load_model(model_path)
    
    predict_text = tf.constant([])
    if(isinstance(sentence, list)):
        predict_text = tf.constant(sentence)
    else:
        predict_text = tf.constant([sentence])

    prediction = loaded_model.predict(predict_text)
    print("Sentence:\n", sentence)
    print("Prediction: \n", prediction)
```

In above code, we have defined a function `predict_sentiment` which takes the path of the saved model and the sentence as an argument. We then load the saved model using the `load_model` function and predict the sentiment of the given sentence using the `predict` function of the loaded model. Finally, we print the sentence and the predicted sentiment.

## Trained Model Predict Movie Review
Here, I choosed sample sentences which are positive and negative from test data of IMDb movie review. Below is testing result.

### Positive Sentiment

``` text
My boyfriend and I went to watch The Guardian.At first I didn't want to watch it, but I loved the movie- It was definitely the best movie I have seen in sometime.They portrayed the USCG very well, it really showed me what they do and I think they should really be appreciated more.Not only did it teach but it was a really good movie. The movie shows what the really do and how hard the job is.I think being a USCG would be challenging and very scary. It was a great movie all around. I would suggest this movie for anyone to see.The ending broke my heart but I know why he did it. The storyline was great I give it 2 thumbs up. I cried it was very emotional, I would give it a 20 if I could!
```

The positive movie review text prediction is: 0.9982441, which means the sentiment of the given text is positive.

{% image /assets/images/ai-ml/sentiment-analysis-predict-positive.png,  alt=Sentiment Analysis Predict Positive Movie Review %}

### Negative Sentiment

``` text
Once again Mr. Costner has dragged out a movie for far longer than necessary. Aside from the terrific sea rescue sequences, of which there are very few I just did not care about any of the characters. Most of us have ghosts in the closet, and Costner's character are realized early on, and then forgotten until much later, by which time I did not care. The character we should really care about is a very cocky, overconfident Ashton Kutcher. The problem is he comes off as kid who thinks he's better than anyone else around him and shows no signs of a cluttered closet. His only obstacle appears to be winning over Costner. Finally when we are well past the half way point of this stinker, Costner tells us all about Kutcher's ghosts. We are told why Kutcher is driven to be the best with no prior inkling or foreshadowing. No magic here, it was all I could do to keep from turning it off an hour in.
```

The negative movie review text prediction is: 0.1066813, which means the sentiment of the given text is negative.

{% image /assets/images/ai-ml/sentiment-analysis-predict-negative.png, alt=Sentiment Analysis Predict Negative Movie Review %}

Fro the movie review text, the model predicts the sentiment as positive and negative with high accuracy.

## AWS Comprehend Predict Movie Review
Below screenshot result is from AWS Comprehend Sentiment Analysis by using the same text. The result is similar.

### Positive
{% image /assets/images/ai-ml/aws-comprehend-predict-positive.png, width=600px, alt=AWS Comprehend Predict Positive Movie Review %}

### Negative
{% image /assets/images/ai-ml/aws-comprehend-predict-negative.png, width=600px, alt=AWS Comprehend Predict Negative Movie Review %}

## Trained Model Predict Short Text
Our trained model is trained by the more words and sentences of the movie review. It's not trained on short text. So, let's test our trained model on a short text. Below is a short negative text.

``` text
Overall, this movie fell short of expectations, failing to deliver on its promise and leaving viewers disappointed.
```

The short negative text prediction is: 0.8730284, it is biger than 0.5, which means the sentiment of the given text prediction is wrong. The text is negative, but the model prediction is positive.

Let's use more than 50 words movie review text to test our trained model. Below negative movie review text is generated by the `ChatGPT`, not from the IMDb movie review test dataset.

``` text
The film started with promising potential but quickly unraveled into a tedious and confusing mess. The plot was convoluted, jumping between disconnected storylines without coherence. Characters lacked depth, their motivations unclear and actions inexplicable. Despite a talented cast, performances felt uninspired, failing to salvage a script riddled with clichés and predictable twists. Overall, it was a disappointing experience, leaving viewers bewildered and dissatisfied.
```

Above text prediction is: 0.02043398, which means the sentiment of the given text is very negative. The model prediction result as expected.

## Trained Model Predict Other Domain Text
In the last, let's try to use the model to predict other domain instead of the movie review. Let's use the `ChatGPT` to generate a more than 50 words about travel feedback.

``` text
My vacation experience was marred by a series of unfortunate events. From delayed flights and lost luggage to subpar accommodations and unhelpful staff, every aspect seemed plagued by mishaps. The tourist attractions were overcrowded, making it impossible to enjoy any peaceful moments. Despite meticulous planning, the trip turned into a stressful ordeal, leaving me wishing I had stayed home instead.
```

The text prediction is: 0.3355006, the prediction is negative. It's correct. However, compare to the movie review text, the model is not trained on the travel feedback text. It's not a good result as we want.

## Conclusion
In this post, we have introduced the concept of sentiment analysis and trained a model using the movie review dataset. We have also tested the trained model on movie review and travel feedback text. Beside, we also compared the result with AWS Comprehend Sentiment Analysis. We have seen that our trained model is not good at predicting the sentiment of short text. We need to train the model on more data to improve the accuracy of the prediction. And for the other domain text, like travel feedback, since our model is not trained on this domain, it's not a good result. The model also need to be trained on more data to improve the accuracy of the prediction.







