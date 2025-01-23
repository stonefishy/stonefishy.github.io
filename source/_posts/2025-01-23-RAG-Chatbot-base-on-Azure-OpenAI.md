---
title: A RAG Chatbot base on Azure OpenAI
date: 2025-01-23 11:31:28
categories: AI/ML
tags: [AI, Chatbot, OpenAI, Python, LangChain, Streamlit, FAISS]
swiper: true
top: true
swiperImg: "/medias/bg-images/v2-b44931ea513e5f7b3b06f2703f79653c_1440w.webp"
---

## Introduction

In this article, we will build a `RAG (Retrieval-Augmented Generation)` chatbot using Azure OpenAI's GPT-4 language model. We will use `Python`, `LangChain` and the `Streamlit` library to build the chatbot interface. Vector store will use `FAISS` library to store the text chunks and metadata.

## What is RAG?

`RAG` is a technique that combines retrieval-based methods with generative models to enhance the quality and relevance of the information produced by AI systems. Here's a breakdown of the two components:

1. **Retrieval**: The model first retrieves relevant documents, text, or data from a knowledge base, database, or external source using information retrieval techniques. This allows the model to access specialized or domain-specific knowledge, which it might not have inherently learned during training.

2. **Generation**: After retrieving relevant information, the model then uses a generative language model (like GPT-3 or GPT-4) to create a response that is coherent, contextually appropriate, and informed by the retrieved content. This allows the AI to answer questions, generate text, or assist in decision-making with enhanced accuracy and knowledge.

## Technical Stack
To build the chatbot, we will use the following techiniques:

- **Azure OpenAI GPT-4**: We will use the GPT-4 language model from Azure OpenAI to generate responses. GPT-4 is a transformer-based language model that is capable of generating coherent and diverse text.
- **LangChain**: LangChain is a Python library that allows us to use the GPT-4 model from Azure OpenAI in our chatbot. LangChain provides a simple interface for building chatbots using GPT-4.
- **Streamlit**: We will use Streamlit to build the chatbot interface. Streamlit is a framework for building web applications in Python. It allows us to create a user-friendly interface for our chatbot.
- **FAISS**: FAISS (Facebook AI Similarity Search) is an open-source library developed by Facebook AI Research. It's designed for efficient similarity search and clustering of high-dimensional data, such as vectors. The primary use case for FAISS is in applications where you need to search for the most similar items to a given query item in large datasets of vectors

## Prerequisites

Before we start, make sure you have the following prerequisites:

- An Azure account
- Python 3.6 or higher
- An IDE or text editor
- A knowledge base or dataset of relevant information

## Setting up the Environment

To set up the environment, we will need to install the following important libraries:

- LangChain
- Streamlit
- Azure OpenAI GPT-4

And also include the `FAISS` and `PyPDF2` libraries. The `FAISS` library is used for efficient similarity search, and the `PyPDF2` library is used to extract text from PDF files.

Below is the entire python libraries:

```text
streamlit==1.40.0
PyPDF2==3.0.1
faiss-cpu==1.9.0
openai==1.59.6
tiktoken==0.8.0
langchain==0.3.14
langchain-community==0.3.14
langchain-core==0.3.29
langchain-openai==0.3.0
langchain-text-splitters==0.3.5
```

## Process PDFs
Here we will read the PDF files and extract the text from them. We will use the `PyPDF2` library to extract the text from the PDF files. And then save it into local vector store which can be used for similarity search by FAISS. The entire code is below:

```python
import os
from pathlib import Path
from PyPDF2 import PdfReader
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain_community.vectorstores import FAISS
from langchain_openai import AzureOpenAIEmbeddings
from dotenv import load_dotenv
import config

load_dotenv()

azure_endpoint = os.getenv("AZURE_OPENAI_ENDPOINT")
api_version = os.getenv("AZURE_OPENAI_API_VERSION")
api_key = os.getenv("OPENAI_API_KEY")
embedding_deployment = os.getenv("AZURE_OPENAI_EMBEDDING_DEPLOYMENT")
chat_deployment = os.getenv("AZURE_OPENAI_CHAT_DEPLOYMENT")


def embeddings():
    # generating embedding
    return AzureOpenAIEmbeddings(
        azure_deployment=embedding_deployment,
        api_version=api_version,
        api_key=api_key,
        azure_endpoint=azure_endpoint)


def get_all_files(directory):
    path = Path(os.path.join(os.getcwd(), directory))
    if not path.exists():
        raise Exception(f"Directory {path.absolute()} does not exist")
    return [str(file) for file in path.rglob("*") if file.is_file()]
    

def process_pdfs(pdfs_directory, vector_store_folder_path, vector_store_index_name):
    pdf_files = get_all_files(pdfs_directory)
    text = ""
    for pdf_file in pdf_files:
        # Read pdf file
        pdf_reader = PdfReader(pdf_file)
        for page in pdf_reader.pages:
            text += page.extract_text()

    # Break it into chunks
    text_splitter = RecursiveCharacterTextSplitter(
        separators="\n",
        chunk_size=1000,
        chunk_overlap=150,
        length_function=len
    )
    chunks = text_splitter.split_text(text)

    # Creating vector store - FAISS
    vector_store = FAISS.from_texts(chunks, embeddings())
    vector_store.save_local(vector_store_folder_path, vector_store_index_name)
    print(f"Processed PDF documents to vector store path `{vector_store_folder_path}`")

if __name__ == "__main__":
    process_pdfs(config.pdfs_directory, config.vector_store_folder_path, config.vector_store_index_name)

```

In above code, we are reading the PDF files and extracting the text from them. We are using the `RecursiveCharacterTextSplitter` to break the text into chunks of 1000 characters with 150 characters overlap. And then using the `FAISS` library to create a vector store of the text chunks. The vector store is saved in the local file system. The vector store files contains two files (`*.faiss` and `*.pkl`) which can be used for similarity search.

The `*.faiss` file contains the vector representation of the text chunks. The `*.pkl` file contains the metadata of the text chunks.

{% image  /assets/images/ai-ml/rag-chatbot-vector-store.png, alt="RAG Chatbot Vector Store" %}

You may noticing that we are using the `dotenv` library to load the environment variables. You can create a `.env` file in the root directory of your project and add the following variables:

``` text
AZURE_OPENAI_ENDPOINT=<your_endpoint_url>
AZURE_OPENAI_API_VERSION=<your_api_version>
OPENAI_API_KEY=<your_api_key?
AZURE_OPENAI_CHAT_DEPLOYMENT=gpt-4o # we are using gpt-4o model, you can use any other model which you deployed in your endpoint.
AZURE_OPENAI_EMBEDDING_DEPLOYMENT=text-embedding-ada-002 # we are using text-embedding-ada-002 model.
```

## Building the Chatbot
After we have processed the PDF files and created the vector store, we can now build the chatbot using LangChain.

First, we will create a `LangChain` instance and load the vector store. Using `FAISS.load_local` method, we can load the vector store from the local file system with the same embeddings model.

```python
def embeddings():
    # generating embedding
    return AzureOpenAIEmbeddings(
        azure_deployment=embedding_deployment,
        api_version=api_version,
        api_key=api_key,
        azure_endpoint=azure_endpoint)


def load_vector_store(vector_store_folder_path, vectore_store_name):
    vector_store = FAISS.load_local(
        folder_path=vector_store_folder_path, 
        embeddings=embeddings(), 
        index_name=vectore_store_name,
        allow_dangerous_deserialization=True)
    return vector_store 
```

Then, we will create a `AzureChatOpenAI` instance and load the chatbot model. We will use the `AzureChatOpenAI` class to interact with the GPT-4 model from Azure OpenAI. Using `load_qa_chain` method which is provided by `LangChain`, we can load the chatbot model from the Azure OpenAI.

```python
def generateChain():
    llm =  AzureChatOpenAI(
        azure_endpoint=azure_endpoint,
        api_key=api_key,
        api_version=api_version,
        azure_deployment=chat_deployment,
        temperature=0,
        max_tokens=1000,
    )
    # chain -> take the question, get relevant document, pass it to the LLM, generate the output
    chain = load_qa_chain(llm, chain_type="stuff")
    return chain
```

After the chain and the vector store are loaded, we can use FAISS vector store to similarity search the user input and retrieve the most relevant document. Passing the retrieved document to the LangChain chain which loads the AzureChatOpenAI to generate the response. Below is core code:

``` python
vector_store = load_vector_store(config.vector_store_folder_path, config.vector_store_index_name)
chain = generateChain()
match_documents = vector_store.similarity_search(prompt)
response = chain.run(input_documents = match_documents, question = prompt)
```

The `chain.run` method takes the input documents and the question as input and returns the generated response. The `match_documents` variable contains the most relevant documents retrieved from the vector store. The `response` variable contains the generated response from the chatbot.

Finally, we will create a Streamlit interface to interact with the chatbot. We will use the `streamlit` library to create a user-friendly interface for our chatbot. For the `streamlit` library usage, you can refer to the official documentation. https://docs.streamlit.io/

```python
def main():
    st.set_page_config(
        page_title="JBL Products Chatbot",
        page_icon="🧊",
        layout="centered",
        initial_sidebar_state="expanded",
    )
    
    st.header("JBL Products Chatbot")
    vector_store = load_vector_store(config.vector_store_folder_path, config.vector_store_index_name)
    chain = generateChain()

    with st.chat_message("assistant"):
        st.markdown("Ask me anything about JBL devices (JBL Pulse 5, JBL Clip 5, JBL Bar 500)")

    if "messages" not in st.session_state:
        st.session_state.messages = []

    for message in st.session_state.messages:
        with st.chat_message(message["role"]):
            st.markdown(message["content"])

    if prompt := st.chat_input("Ask me something"):
        st.chat_message("user").markdown(prompt)
        st.session_state.messages.append({"role": "user", "content": prompt})

        response = "I am thinking..."
        with st.spinner(response):
            match_documents = vector_store.similarity_search(prompt)
            response = chain.run(input_documents = match_documents, question = prompt)

        with st.chat_message("assistant"):
            st.write_stream(generate_stream(response))
        st.session_state.messages.append({"role": "assistant", "content": response})
```

In above code, we are using the `st.chat_message` method to create a chat message with a specific role, using the `st.chat_input` method to get the user input. And using the `st.spinner` method to show a loading spinner while the chatbot is generating the response. Finally using the `st.write_stream` method to write the response to the chat message.

For the stream response, actually we mock it by using the `generate_stream` method. This method is used to generate the stream response. We can use the `st.write_stream` method to write the stream response to the chat message. This will let the chatbot to print word one by one in the chat message.

``` python
def generate_stream(response):
    # generate the stream of messages
    for word in response.split(" "):
        yield word + " "
        time.sleep(0.05)
```

Below is chatbot conversational interface:

{% image /assets/images/ai-ml/rag-chatbot-streamlit-interface.png, alt="RAG Chatbot Streamlit Interface" %}

## Conclusion
In this article, we have built a `RAG (Retrieval-Augmented Generation)` chatbot using Azure OpenAI's GPT-4 language model. We have used Python, LangChain and the Streamlit library to build the chatbot interface. We have also processed the PDF files and created the vector store using FAISS library. The pdf files is crawled from the website https://www.manua.ls

For the entire code, you can refer to the Github repository: https://github.com/stonefishy/rag-chatbot. Please don't forget to star the repository if you find it useful. Thank you for reading.