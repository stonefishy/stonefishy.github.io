---
title: RAG Chatbot - Converting PDF Document to Vector
date: 2025-02-24 16:27:50
categories: AI/ML
tags: [AI, OpenAI, Python, FAISS, Langchain, Chatbot, RAG]
top: true
swiper: true
---

## Introduction
In this series of blogs, we will build a `RAG(Retrieval-Augmented Generation)` chatbot which using `WiX(WiXToolset)` documents as knowledge data. It will contains below steps:
1. Converting PDF Document to Vector
2. Building a Chatbot that can answer questions based on the documents

The technology stack used in this project are
- Python
- LangChain
- OpenAI
- Chainlit
- FAISS

In this blog, we will focus on the first step, Converting PDF Document to Vector. 

## Vector
The vector is a mathematical representation of a document or a text. It is a numerical representation of the text that can be used for various natural language processing tasks. The vector can be generated using various techniques like Bag-of-Words, TF-IDF, Word2Vec, etc. To store the vector, we can use various databases like FAISS, Pinecone, chroma etc.

## PDF Document to Vector
### FAISS
In this blog, we will use `FAISS(Facebook AI Similarity Search)` to save the vector on local. The FAISS is  is a library for efficient similarity search and clustering of dense vectors. It is developed by Faiss Team at Facebook AI Research. FAISS is designed to handle large-scale nearest neighbor searches, which are common in applications like recommendation systems, image retrieval, and natural language processing. The library provides multiple algorithms for searching and clustering, including exact and approximate methods, and is optimized for both speed and accuracy. It supports various types of vector norms and can be used on both CPU and GPU for fast computation.

### Prepare the PDF documents
For the WiX documents, we can download the documents from the official website https://docs.firegiant.com/wix/fivefour/. And here we only use the WiX upgrade guide document. Below are documents we are using:
{% image /assets/images/ai-ml/faiss-wix-pdfs.png %}

### Extracting Text from PDF Documents
We will using the library `PyPDF2` to extract the text from the PDF documents. Below is code snippet to extract all the text from the PDF documents and store it in a text variable. 

``` python
pdf_directory = Path(pdf_storage_path)
text = ""

for pdf_path in pdf_directory.glob("*.pdf"):
    print(f"Reading document {pdf_path}")
    pdf_reader = PdfReader(pdf_path, True)
    pdf_text = ""
    for page in pdf_reader.pages:
        page_text = page.extract_text()
        pdf_text += page_text

    txt_path = pdf_path.with_name(pdf_path.stem + ".txt")
    with open(txt_path, "w", encoding="utf-8") as file:
        file.write(pdf_text)
    text += pdf_text + "\n\n" 
```

### Embeddings
Using the `AzureOpenAIEmbeddings` class from `langchain` library to get the vector representation of the text. For the Azuer OpenAI endpoints and api keys you can define it in the `.env` file.

.env file
``` python
AZURE_OPENAI_ENDPOINT=
AZURE_OPENAI_API_KEY=
AZURE_OPENAI_CHAT_DEPLOYMENT_NAME=gpt-4o
AZURE_OPENAI_CHAT_DEPLOYMENT_VERSION=2023-07-01-preview
AZURE_OPENAI_ADA_EMBEDDING_MODEL_NAME=text-embedding-ada-002
AZURE_OPENAI_ADA_EMBEDDING_DEPLOYMENT_NAME=text-embedding-ada-002
AZURE_OPENAI_ADA_DEPLOYMENT_VERSION=2023-07-01-preview
```

``` python
embeddings = AzureOpenAIEmbeddings(
    deployment=AZURE_OPENAI_ADA_EMBEDDING_DEPLOYMENT_NAME,
    model=AZURE_OPENAI_ADA_EMBEDDING_MODEL_NAME,
    azure_endpoint=AZURE_OPENAI_ENDPOINT,
    openai_api_key=AZURE_OPENAI_API_KEY,
    openai_api_version=AZURE_OPENAI_ADA_DEPLOYMENT_VERSION
)
```

### Construct FAISS
After we have extracted the text from the PDF documents, below code snippet will construct the FAISS index. Using the `RecursiveCharacterTextSplitter` class from `langchain` library to split the text into chunks. Using `AzureOpenAIEmbeddings` class from `langchain` library, we can get the vector representation of the text. And save it into the FAISS index.

``` python
text_splitter = RecursiveCharacterTextSplitter(
    separators="\n",
    chunk_size=1000, 
    chunk_overlap=250,
    length_function=len
    )

print("splitting text into chunks...")
chunks = text_splitter.split_text(text)

print("embedding all documents...")
vector_store =FAISS.from_texts(chunks, embeddings)
vector_store.save_local("./vector_stores", index_name)

print("finished processing all pdfs!")
```

So the entire workflow is that we have downloaded the PDF documents, extracted the text from the PDF documents, and constructed the FAISS index. The FAISS index will be saved in the local directory.

Below is the function code snippet:

``` python
def pdfs_to_faiss(pdf_storage_path: str, index_name: str):
    pdf_directory = Path(pdf_storage_path)
    text = ""
    text_splitter = RecursiveCharacterTextSplitter(
        separators="\n",
        chunk_size=1000, 
        chunk_overlap=250,
        length_function=len
        )
    
    for pdf_path in pdf_directory.glob("*.pdf"):
        print(f"Reading document {pdf_path}")
        pdf_reader = PdfReader(pdf_path, True)
        pdf_text = ""
        for page in pdf_reader.pages:
            page_text = page.extract_text()
            pdf_text += page_text

        txt_path = pdf_path.with_name(pdf_path.stem + ".txt")
        with open(txt_path, "w", encoding="utf-8") as file:
            file.write(pdf_text)
        text += pdf_text + "\n\n" 

    print("splitting text into chunks...")
    chunks = text_splitter.split_text(text)
    
    print("embedding all documents...")
    vector_store =FAISS.from_texts(chunks, embeddings)
    vector_store.save_local("./vector_stores", index_name)

    print("finished processing all pdfs!")
```

There is also another way to construct the FAISS index using the `langchain` library from pdf documents. Using `PyMuPDFLoader` to load the pdf documents instead of extracting the text from the pdf documents. Below is the code snippet:

``` python 
def pdfs_to_faiss2(pdf_storage_path: str, index_name: str):
    pdf_directory = Path(pdf_storage_path)
    docs = []
    text_splitter = RecursiveCharacterTextSplitter(
        separators=["\n\n", "\n", " "],
        chunk_size=1000, 
        chunk_overlap=250,
        length_function=len
        )
    
    for pdf_path in pdf_directory.glob("*.pdf"):
        print(f"Reading document {pdf_path}")
        loader = PyMuPDFLoader(str(pdf_path))
        documents = loader.load()
        pdf_docs= text_splitter.split_documents(documents)
        docs += pdf_docs
    
    print("embedding all documents...")
    vector_store =FAISS.from_documents(docs, embeddings)
    vector_store.save_local("./vector_stores", index_name)

    print("finished processing all pdfs!")
```

Above two code snippets are reading all documents from one directory and saving them into the FAISS index. But somehow, if the text is too long and exceed the maximum length of the Azure OpenAI API, it failed to process the text. Refer to Azure OpenAI API documentation https://learn.microsoft.com/en-us/azure/ai-services/openai/quotas-limits. 

We can count the token by using `tiktoken` library. The model is used to tokenize the text. If the model is not found, it defaults to using the 'cl100k_base' tokenizer. Here we can passing the AzureOpenAIEmbeddings model to the `count_tokens` function to count the number of tokens in the text.

```python

"""
Counts the number of tokens in a given text using the specified model's tokenizer.
If the model is not found, it defaults to using the 'cl100k_base' tokenizer.

Args:
    text (str): The input text to be tokenized.
    model (str): The name of the model for which the tokenizer is used.

Returns:
    int: The total number of tokens in the text.
"""
def count_tokens(text, model):
    try:
        encoding = tiktoken.encoding_for_model(model)
    except KeyError:
        print("Warning: model not found. Using cl100k_base encoding.")
        encoding = tiktoken.get_encoding("cl100k_base")
    num_tokens = len(encoding.encode(text))
    print(f"Total tokens: {num_tokens}")
    return num_tokens
```

### Mergging Indexes
The FAISS supports to merge multiple indexes into one index. So we can merge the index of all the documents into one index. Below is the code snippet to merge the index of all the documents into one index.

Let's write another function to embedding pdfs into individual index. Below function will embedding all the pdfs into individual index and save it in the specified directory.

``` python
def pdfs_to_faiss_files(pdf_storage_path: str, faiss_dir_path: str):
    pdf_directory = Path(pdf_storage_path)
    text_splitter = RecursiveCharacterTextSplitter(
        separators=["\n\n", "\n", " "],
        chunk_size=1000, 
        chunk_overlap=200,
        length_function=len
        )

    for pdf_path in pdf_directory.glob("*.pdf"):
        print(f"Reading document {pdf_path}")
        loader = PyMuPDFLoader(str(pdf_path))
        documents = loader.load()
        pdf_docs= text_splitter.split_documents(documents)

        print(f"embedding document {pdf_path}")
        vector_store =FAISS.from_documents(pdf_docs, embeddings)
        vector_store.save_local(faiss_dir_path, pdf_path.stem)
        print(f"finished processing pdf {pdf_path}!")
```

Call this function with below code snippet to embedding all the pdfs into individual index.

``` python
    pdfs_to_faiss_files(PDF_STORAGE_PATH, "./faiss_files")
```

{% image /assets/images/ai-ml/faiss-wix-pdfs-indexing.png %}

{% image /assets/images/ai-ml/faiss-wix-indexes.png %}

After these indexes are created, we can use FAISS to merge them into one index. Below is the code snippet to merge the index of all the documents into one index.

``` python
"""
Merges multiple FAISS files into a single index.

Args:
    faiss_dir_path (str): The path to the directory containing the FAISS files.
    index_name (str): The name of the index to be saved.
"""
def merge_faiss_files(faiss_dir_path: str, index_name: str):
    faiss_directory = Path(faiss_dir_path)
    
    is_first_faiss = True
    first_faiss = None
    for faiss_path in faiss_directory.glob("*.faiss"):
        print(f"Loading FAISS file {faiss_path}")
        if is_first_faiss:
            is_first_faiss = False
            first_faiss = FAISS.load_local(
                folder_path=faiss_dir_path, 
                embeddings=embeddings, 
                index_name=faiss_path.stem,
                allow_dangerous_deserialization=True)
        else:
            print(f"Merging with {faiss_path}")
            first_faiss.merge_from(FAISS.load_local(
                folder_path=faiss_dir_path, 
                embeddings=embeddings, 
                index_name=faiss_path.stem,
                allow_dangerous_deserialization=True))

    first_faiss.save_local("./vector_stores", index_name)
```

Call this function with below code snippet to merge all the indexes into one index. We merged all `FAISS` index files which are saved in the `faiss_files` directory into one index.

``` python
merge_faiss_files("./faiss_files", INDEX_NAME)
```

{% image /assets/images/ai-ml/faiss-wix-merging-indexings.png %}

{% image /assets/images/ai-ml/faiss-wix-merged-index.png %}

Next step, we will build a WiX chatbot which loading this `FAISS` index and passing the question and retrieving the relevant documents from the index. For the entire code, you can check the github https://github.com/stonefishy/WiX5-chatbot to see the entire code.

