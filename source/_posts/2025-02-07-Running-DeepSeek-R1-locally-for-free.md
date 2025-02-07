---
title: Running DeepSeek-R1 locally for free
date: 2025-02-07 10:16:27
categories: AI/ML
tags: [AI, DeepSeek]
---

## Introduction

{% image  /assets/images/ai-ml/deepseek-logo.png, width="800px", alt="DeepSeek" %}

The `DeepSeek` recently is very popular. The application download is on topest of the app store globally. The deepseek has several models, like `deepseek-r1`, `deepseek-coder` and `deepseek-v3` models etc. It's all open source and free to use.

- `deepseek-r1`: DeepSeek's first-generation of reasoning models with comparable performance to OpenAI-o1, including six dense models distilled from DeepSeek-R1 based on Llama and Qwen.
- `deepseek-coder`: It is a capable coding model trained on two trillion code and natural language tokens.
- `deepseek-v3`: A strong Mixture-of-Experts (MoE) language model with 671B total parameters with 37B activated for each token.

Here we will run the `deepseek-r1` model locally. It's very easy and setup it quickly. Let's get started.

## Ollama download and installation
The first step is to download the `Ollama` and install it on your local machine. It supports Windows, Linux and MacOS. You can download the latest version from the official website. https://ollama.com/

{% image  /assets/images/ai-ml/ollama.png, width="800px", alt="Ollama" %}

After download and install it, you can check the version or commands from terminal or command-line.

``` bash
## Check the version of Ollama
ollama --version

## Check the commands of Ollama
ollama --help
```

{% image /assets/images/ai-ml/ollama-version-commands.png, alt="Ollama version and commands" %}

## Running the deepseek-r1 model
Now, we can run the `deepseek-r1` model using Ollama. We need to download this model by using ollama. The `deepseek-r1` model contains serveral models, 1.5b, 7b, 8b, 14b, 32b, 70b and even 671b. For general computer performance, suggestion to use 1.5b model. I tried the 8b model on my local. It can run but the response is slowlly and memory is up to 90%. The 1.5b model running smoothly and response is fast. My local machine has 16GB RAM and i7 processor.

We can use the following command to download the `deepseek-r1` model.

``` bash
ollama pull deepseek-r1:1.5b
```

we also can run this model directly, if the model not exist, it will download automatically.

``` bash
ollama run deepseek-r1:1.5b
```

{% image /assets/images/ai-ml/ollama-run-deepseek-r1.png, alt="Ollama run deepseek-r1" %}

## Ask anything in deepseek
Now, we can ask anything in deepseek. Just type the question and press enter. The model will answer the question. See below screenshot

{% image /assets/images/ai-ml/ollama-deepseek-answer-1.png, alt="DeepSeek-R1 Answer" %}

{% image /assets/images/ai-ml/ollama-deepseek-answer-2.png, alt="DeepSeek-R1 Answer" %}

See, it's easy to run the `deepseek-r1` model locally. You can also run other models like `deepseek-coder` and `deepseek-v3` models, or `llama` model. The `Ollama` models contains many open source models, you can use it for free.
