---
title: Deploy User Friendly AI Interface Chatbot on Local by Using Ollama + Open-WebUI.
date: 2025-02-10 09:55:11
categories: AI/ML
tags: [AI, Ollama, DeepSeek, Gemma, Open-WebUI, Chatbot]
swiper: true
---

In Previous blog, we talk about how to running `DeepSeek` large language model (LLM) on local machine by using Ollama. For installing Ollama, you can check the previous blog. We play it and chat on terminal. In this blog, we will talk about how to deploy user-friendly AI interface chatbot on local machine by using Ollama and Open-WebUI.

## What is Open-WebUI?

`Open WebUI` is an extensible, feature-rich, and user-friendly self-hosted AI platform designed to operate entirely offline. It supports various LLM runners like `Ollama` and `OpenAI-compatible APIs`, with built-in inference engine for RAG, making it a powerful AI deployment solution.

## Install Ollama
To install the Ollama, you can download the `Ollama` from official website and follow the installation guide. Once installed, you can check the ollama version and commands using below commands in your terminal.

``` bash
ollama --version
ollama --help
```

{% image /assets/images/ai-ml/ollama-version-commands.png %}

For downloading the `LLM` model, using below command `ollama pull <model_name>`in your terminal.

``` bash
ollama pull deepseek-r1:1.5b
```

I already downloaded the `deepseek` and `gemma` models in my local machine. You can check the downloaded models using `ollama ls` command.

``` bash
ollama ls
```

{% image /assets/images/ai-ml/ollama-models-list.png %}


## Set up Open-WebUI
After you setup the Ollama, to set up Open-WebUI, there are multiple ways
### Docker way
If you have installed `Docker` on your local, you can easily launch Open-WebUI using below command.
 
``` bash
docker run -d -p 3000:8080 --add-host=host.docker.internal:host-gateway -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main
```

Once it done, access the Open-WebUI by using `http://localhost:3000` in your browser.

### Python pip way
The second way is to install Open-WebUI by using Python pip and launch it locally. It requires `Python 3.11` to avoid the compatibility issues.

Check Python version, ensure the version is 3.11 or above.
``` bash
python --version
```

Next, install Open-WebUI using pip.
``` bash
pip install open-webui
```

When you install Open-WebUI by using python pip command, if you local machine  Microsoft Visual C++ compiler version is lower than 14, you may encounter the below error.

``` error
Building wheels for collected packages: chroma-hnswlib
  Building wheel for chroma-hnswlib (pyproject.toml) ... error
  error: subprocess-exited-with-error

  × Building wheel for chroma-hnswlib (pyproject.toml) did not run successfully.
  │ exit code: 1
  ╰─> [5 lines of output]
      running bdist_wheel
      running build
      running build_ext
      building 'hnswlib' extension
      error: Microsoft Visual C++ 14.0 or greater is required. Get it with "Microsoft C++ Build Tools": https://visualstudio.microsoft.com/visual-cpp-build-tools/
      [end of output]

  note: This error originates from a subprocess, and is likely not a problem with pip.
  ERROR: Failed building wheel for chroma-hnswlib
Failed to build chroma-hnswlib
```

{% image /assets/images/ai-ml/open-webui-pip-install-error.png %}.

To fix this issue, you can install the latest version of C++ compiler and re-install the Open-WebUI. Download the Microsoft C++ Build Tools from the official website https://visualstudio.microsoft.com/visual-cpp-build-tools/.

Install the `Desktop development with C++`.

{% image /assets/images/ai-ml/open-webui-pip-error-resolved.png %}

After the latest C++ compiler installed, re-install `Open-WebUI` again, once all completed, launch the Open-WebUI using below command.

``` bash
open-webui serve
```

This will start the Open WebUI server, which we can access at http://localhost:8080

{% image /assets/images/ai-ml/open-webui-serve.png %}

Access the Open-WebUI by using `http://localhost:8080` in your browser. And create an admin account. Then you can start to use the `Open-WebUI`. 
I have already installed some LLM models from `Ollama`, so we can see there are several models listed in the `Open-WebUI`.

{% image /assets/images/ai-ml/open-webui-models.png %}

## Have Fun with Open-WebUI
Let's try to use `Gemma` model and `DeepSeek` model to chat with Open-WebUI.

### Using Gemma model
To use the `Gemma` model, select the `Gemma` model from the `Models` dropdown list and start to chat with the chatbot. Let's use the `gemma:2b` model to chat.
If the model is not listed, using `Ollama` to download the model first.

{% image /assets/images/ai-ml/open-webui-gemma-model.png %}

### Using DeepSeek model
To use the `DeepSeek` model, select the `DeepSeek` model from the `Models` dropdown list and start to chat with the chatbot. Let's use the `deepseek-r1:1.5b` model to chat.

{% image /assets/images/ai-ml/open-webui-deepseek-model.png %}

We can see the results of the `gemma:2b` model and the `deepseek-r1:1.5b` model are different. The `gemma:2b` model is just decreased in quality, however, the `deepseek-r1:1.5b` model is more accurate. It takes some time to deep thinking, and answer it carefully.

In `DeepSeek` model, we can see the thinking process of the deepseek model. It is a good way to understand how the deepseek thinking.

{% image /assets/images/ai-ml/open-webui-deepseek-thinking.png %}

## Conclusion
In this blog, we have talked about how to deploy user-friendly AI interface chatbot on local machine by using Ollama and Open-WebUI. We have also used the `Gemma` and `DeepSeek` models to chat with the chatbot. We can see the results of the `gemma:2b` model and the `deepseek-r1:1.5b` model are different. The `gemma:2b` model is just decreased in quality, however, the `deepseek-r1:1.5b` model is more accurate. It takes some time to deep thinking, and answer it carefully.

Hope you like it. have fun to use `Open-WebUI`.


