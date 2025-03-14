---
title: Create your own OpenAI endpoints in Azure Cloud.
date: 2025-03-14 15:17:59
categories: AI/ML
tags: [AI, OpenAI, Azure, Cloud]
---

Previously, we have introduced how to use Azure OpenAI to develop a RAG (Retrieval-Augmented Generation) chatbot. In this article, we will learn how to create your own OpenAI endpoints in `Azure Cloud`.

`Azure OpenAI` is a service provided by Microsoft Azure that allows businesses and developers to integrate OpenAI's advanced AI models into their applications and systems. This service gives users access to powerful language models like `GPT-3`, `GPT-4`, and other AI models developed by OpenAI, via an API hosted on Microsoft's Azure cloud platform.

To use Azure OpenAI, users need an Azure subscription and access to the OpenAI API via Azure. Assume you already have an Azure Cloud account and an Azure subscription. Here are the steps to create your own OpenAI endpoints in Azure Cloud

## Step 1: Create Azure OpenAI Resource

In Microsoft Azure Cloud, Open the `Azure AI Services` and create a `Azure OpenAI` resource. Select your subscription and resource group, create a new resource group if you don't have one. Fill the endpoint name and select the pricing tier. 

{% image /assets/images/ai-ml/azure-openai-create-1.png %}

In Network, choose the All networks including internet option. because we want to access the OpenAI API from anywhere.

{% image /assets/images/ai-ml/azure-openai-create-2.png %}

For the tags, you can type your own tags for this resource.

{% image /assets/images/ai-ml/azure-openai-create-3.png %}

After that, you can review your resource and create it.

{% image /assets/images/ai-ml/azure-openai-create-4.png %}

Deploying the resource can take a few seconds.

{% image /assets/images/ai-ml/azure-openai-create-5.png %}

Once the resource is deployed, you can select your resource and click the `Endpoints` and `Manage keys` to see the API endpoint and key.

{% image /assets/images/ai-ml/azure-openai-create-6.png %}

You can copy the API endpoint and key and use it in your application to access the OpenAI API.

{% image /assets/images/ai-ml/azure-openai-create-7.png %}

Do you think we have finished to create our own OpenAI endpoints in Azure Cloud? The answer is No, We still need to deploy the OpenAI Model to our Azure OpenAI resource. If we access this API endpoint directly, we will get an error message. like below

```
openai.NotFoundError: Error code: 404 - {'error': {'code': 'DeploymentNotFound', 'message': 'The API deployment for this resource does not exist. If you created the deployment within the last 5 minutes, please wait a moment and try again.'}}
```

## Step 2: Deploy OpenAI Model to Azure OpenAI Resource

To deploy the OpenAI model to our Azure OpenAI resource, open `Azure AI Foundry` and select `Deployments` item in left menu. Click `Deploy model` button and select `Deploy base model`.

{% image /assets/images/ai-ml/azure-openai-create-8.png %}

Let's choose the `GPT-4o` model.

{% image /assets/images/ai-ml/azure-openai-create-9.png %}

In deployment model dialog, let's keep the deployment name and deployment type select as Global Standard.

{% image /assets/images/ai-ml/azure-openai-create-10.png %}

## Step 3: Test the OpenAI Endpoint

Once the model deployment done, we can go to `Playground` menu to test our model.

{% image /assets/images/ai-ml/azure-openai-create-11.png %}

Now, you can use your own OpenAI endpoints in your application without errors. Below is using `RESTMan` extension to call the Azure OpenAI endpoints via REST API way.

{% image /assets/images/ai-ml/azure-openai-create-12.png %}

There are many models in Azure AI Foundry, you can choose the model you want to use.

{% image /assets/images/ai-ml/azure-openai-models.png %}

Hope this article helps you to create your own OpenAI endpoints in Azure Cloud.