---
title: Conversational RAG Chatbot - Build a Chatbot with LangChain and Chainlit
date: 2025-02-27 15:33:13
categories: AI/ML
tags: [AI, OpenAI, Python, FAISS, Langchain, Chainlit Chatbot, RAG]
top: true
swiper: true
swiperImg: "/medias/bg-images/v2-83ea273b88e119d9615aed0d89aad4ab_1440w.webp"
---

In previous blog, we have converting the PDF documents into the `FAISS` vector index, and saved in the local. Now, we will build a RAG chatbot using the `LangChain`, `Chainlit` and `OpenAI` base on previous `FAISS` index.

Before we get started, let's talk about what is `Chainlit` and `LangChain`.

## Chainlit
We choose the `Chainlit` framework to build the chatbot. `Chainlit` is a framework designed to simplify the process of building and deploying chatbots for large language models (`LLMs`). It provides a way to create conversational applications that can interact with users through chat interfaces. Chainlit helps developers to chain together different components of a chatbot, such as the model itself, user interfaces, and data sources, making it easier to build complex conversational systems. It is particularly useful for those who want to integrate LLMs into their existing applications or create standalone chatbots. You can find the `Chainlit` documentation [here](https://docs.chainlit.io/get-started/overview).

## LangChain
`LangChain` is a framework designed to simplify the process of building language models and applications that leverage these models. It provides a set of tools and libraries that allow developers to integrate language models into their applications more easily, handle data flow, and manage the interactions between different components of their system. LangChain supports various language models and can be used to create a wide range of applications, from chatbots and virtual assistants to document summarization and translation tools. It aims to make it straightforward to build, deploy, and scale language-driven applications.

## Get started
Ok, let's get started. First, we need to install the `LangChain` and `Chainlit` and other libraries, all the required libraries are below. Creating a `requirements.txt` file and store below libraries in it:

``` text
openai
langchain
chainlit
faiss-cpu
tiktoken
pymupdf
PyPDF2
langchain_openai
langchain_community
```

I suggest to create a virtual environment for this project before installing these libraries. We can use the following command to create a virtual environment:

``` bash
python -m venv venv
```

After creating the virtual environment, activate it using the following command:
``` bash
source venv/bin/activate
```

Then install all the required libraries using the following command:

``` bash
pip install -r requirements.txt
```

Now, we can start building the chatbot.

## Building Chatbot UI

The `Chainlit` provides the wonderful API to build the chatbot UI. It saves much time and efforts. In the project, create a `app.py` and import the `chainlit` library then write the below code. We will use 3 chainlit annotations. `@cl.set_starters`, `@cl.on_chat_start`, and `@cl.on_message`.

``` python 
import chainlit as cl

@cl.set_starters
async def set_starters():
    # The chatbot starter page which displays the quick list of questions
    return [
        cl.Starter(
            label="The Major Updates in Wix5",
            message="What's the major updates in Wix5?",
            icon="/public/images/specifications.svg",
            ),
        cl.Starter(
            label="Migrate Project from Wix4 to Wix5",
            message="How to mirage the wix4 project to wix5?",
            icon="/public/images/jigsaw.png",
            ),
        cl.Starter(
            label="Build Burn Bootstrapper on Wix5",
            message="How to build a burn bootstrapper on wix5?",
            icon="/public/images/bundle.svg",
            )
        ]


@cl.on_chat_start
async def start():
    # starting the chatbot logic

@cl.on_message
async def main(message: cl.Message):
    # chatbot logic and response here
```

1. The `@cl.set_starters` annotation is used to set the chatbot starter page. It takes a list of `cl.Starter` objects as input. Each `cl.Starter` object represents a question and its corresponding message. The `icon` parameter is used to set the icon for the question.

2. The `@cl.on_chat_start` annotation is used to start the chatbot logic. Such as setting the initial state of the chatbot, loading the chatbot model, FAISS index, create a langchain pipeline, etc.

3. The `@cl.on_message` annotation is used to handle the chatbot logic and response. It takes a `cl.Message` object as input. The `cl.Message` object contains the user's message and other metadata. We can use the `message.content` attribute to get the user's message.

The starter page screenshot like below:

{% image /assets/images/ai-ml/chainlit-chatbot-starter-page.png %, alt="Chainlit Chatbot Starter Page" %}

You can also customize the theme, or do some changes in UI. 

## Loading the FAISS Vector Index
Using `FAISS` library, we can load the `FAISS` vector index. We need to pass the `embeddings` and `index_name` to the `FAISS.load_local` method. The `embeddings` parameter is the embedding model we used before, and the `index_name` parameter is the name of the index. Here, our embedding model is `AzureOpenAIEmbeddings` and the index name is `wix-upgrade`.

``` python
load_dotenv()

VECTOR_INDEX_NAME = 'wix-upgrade'
AZURE_OPENAI_API_KEY = os.getenv("AZURE_OPENAI_API_KEY")
AZURE_OPENAI_ENDPOINT = os.getenv("AZURE_OPENAI_ENDPOINT")
AZURE_OPENAI_ADA_DEPLOYMENT_VERSION = os.getenv("AZURE_OPENAI_ADA_DEPLOYMENT_VERSION")
AZURE_OPENAI_CHAT_DEPLOYMENT_VERSION= os.getenv("AZURE_OPENAI_CHAT_DEPLOYMENT_VERSION")
AZURE_OPENAI_ADA_EMBEDDING_DEPLOYMENT_NAME = os.getenv("AZURE_OPENAI_ADA_EMBEDDING_DEPLOYMENT_NAME")
AZURE_OPENAI_ADA_EMBEDDING_MODEL_NAME = os.getenv("AZURE_OPENAI_ADA_EMBEDDING_MODEL_NAME")
AZURE_OPENAI_CHAT_DEPLOYMENT_NAME = os.getenv("AZURE_OPENAI_CHAT_DEPLOYMENT_NAME")

embeddings = AzureOpenAIEmbeddings(
    deployment=AZURE_OPENAI_ADA_EMBEDDING_DEPLOYMENT_NAME,
    model=AZURE_OPENAI_ADA_EMBEDDING_MODEL_NAME,
    azure_endpoint=AZURE_OPENAI_ENDPOINT,
    openai_api_key=AZURE_OPENAI_API_KEY,
    openai_api_version=AZURE_OPENAI_ADA_DEPLOYMENT_VERSION
)

docs_vector_store = FAISS.load_local(
    folder_path="./vector_stores", 
    embeddings=embeddings, 
    index_name=VECTOR_INDEX_NAME,
    allow_dangerous_deserialization=True)
```

Using `load_dotenv()` method, we can load the environment variables from the `.env` file. We can put the loading vector index code in the `@cl.on_chat_start` annotation.

## Building the Converstaion Chain
After we load the vector index, we can build the conversation chain using `LangChain`. The main logic are below.

``` python
from langchain_openai import AzureOpenAIEmbeddings,AzureChatOpenAI
from langchain_community.vectorstores import FAISS
from langchain_community.chat_message_histories import ChatMessageHistory
from langchain.chains.combine_documents import create_stuff_documents_chain
from langchain.chains import create_history_aware_retriever, create_retrieval_chain
from langchain_core.chat_history import BaseChatMessageHistory
from langchain_core.runnables.history import RunnableWithMessageHistory

chat_history_store = {}
def get_session_history(session_id: str) -> BaseChatMessageHistory:
    if session_id not in chat_history_store:
        chat_history_store[session_id] = ChatMessageHistory()
    return chat_history_store[session_id]

@cl.on_chat_start
async def start():
    docs_vector_store = FAISS.load_local(
        folder_path="./vector_stores", 
        embeddings=embeddings, 
        index_name=VECTOR_INDEX_NAME,
        allow_dangerous_deserialization=True)
    
    llm=AzureChatOpenAI(
        api_key=AZURE_OPENAI_API_KEY,
        azure_endpoint=AZURE_OPENAI_ENDPOINT,
        api_version=AZURE_OPENAI_CHAT_DEPLOYMENT_VERSION,
        openai_api_type="azure",
        azure_deployment=AZURE_OPENAI_CHAT_DEPLOYMENT_NAME,
        streaming=True,
        verbose=True,
    )
    
    retriever = docs_vector_store.as_retriever()
    history_aware_retriever = create_history_aware_retriever(llm, retriever, contextualize_q_prompt)
    question_answer_chain = create_stuff_documents_chain(llm, QA_PROMPT)
    rag_chain = create_retrieval_chain(history_aware_retriever, question_answer_chain)

    conversational_rag_chain = RunnableWithMessageHistory(
        rag_chain,
        get_session_history,
        input_messages_key="input",
        history_messages_key="chat_history",
        output_messages_key="answer",
    )

    cl.user_session.set("chain", conversational_rag_chain)
```

In above code, we first load the `FAISS` vector index and create the `AzureChatOpenAI` language model. We then create the `retriever` using the `docs_vector_store` and create the `history_aware_retriever` using the `create_history_aware_retriever` method. The `contextualize_q_prompt` method is used to create the contextualized question prompt. The `create_stuff_documents_chain` method is used to create the question answer chain. Finally, we create the `rag_chain` using the `create_retrieval_chain` method.

We then create the `conversational_rag_chain` using the `RunnableWithMessageHistory` class. The `get_session_history` method is used to get the chat history for each session. The `input_messages_key`, `history_messages_key`, and `output_messages_key` parameters are used to set the keys for the input, history, and output messages.

We set the `conversational_rag_chain` as the chatbot chain using the `cl.user_session.set` method. We can use it later.

Here you're notice there is `contextualize_q_prompt` and `QA_PROMPT` variable. They are prompts which will passting to LLM to restrict the LLM answers. The code is below:

``` python
from langchain_core.prompts.chat import (
    ChatPromptTemplate,
    MessagesPlaceholder
)

contextualize_q_system_prompt = """
Given a chat history and the latest user question \
which might reference context in the chat history, formulate a standalone question \
which can be understood without the chat history. Do NOT answer the question, \
just reformulate it if needed and otherwise return it as is.
"""
contextualize_q_prompt = ChatPromptTemplate.from_messages(
    [
        ("system", contextualize_q_system_prompt),
        MessagesPlaceholder("chat_history"),
        ("human", "{input}"),
    ]
)


qa_system_prompt = """
Act like a WiX (WixToolset) development expert and help me with related WiX development questions. 

Some **strict rules** you have to follow NO MATTER WHAT:
Only answer related questions about WiX development.
If you don't know the answer, say:
I don't have such information, please refer to WiX offical documentation for the information. https://docs.firegiant.com/

Please Use the following pieces of context to answer the user's question. 
----------------
{context}
"""
QA_PROMPT = ChatPromptTemplate.from_messages(
    [
        ("system", qa_system_prompt),
        MessagesPlaceholder("chat_history"),
        ("human", "{input}"),
    ]
)
```

The `contextualize_q_prompt` is used to create the contextualized question prompt base on chat history and the latest user iput. The `MessagesPlaceholder("chat_history")` is used to insert the chat history in the prompt. From the prompt, It will formulate a standard question base on chat history and the latest user input.

The `qa_system_prompt` is used to create the question answer prompt. It is limit to only answer the related questions about WiX development. If the user's question is not related to WiX development, it will say I don't have such information, please refer to the WiX offical documentation for the information. You can also remove this limitation prompt text to make the AI model reply any question.

So now, our chain is ready to use. Next step we will uset this chain to answer the user's question.

## Invoke Chain
In previous step, we have set the `conversational_rag_chain` as the chatbot chain using the `cl.user_session.set` method. Now, we can use it to answer the user's question. Below is `@cl.on_message` annotation to invoke the chain and send the response.

``` python
@cl.on_message
async def main(message: cl.Message):
    try:
        chain = cl.user_session.get("chain")
        msg = cl.Message(content="")
        res = chain.invoke(
            {"input": message.content}, 
            config={
                "configurable": {"session_id": cl.user_session.get("id")}
            }
        )
        response = res["answer"]

        stream_size = 20
        for i in range(int(round(len(response) / stream_size)) + 1):
            msg.content = response[0 : (i + 1) * stream_size]
            await msg.send()
            time.sleep(0.05)

    except Exception as e:
        print(f"An error occurred: {e}")
        if e.code == "content_filter":
            await cl.ErrorMessage(
                """
                The response was filtered due to the prompt triggering Azure OpenAI's content management policy. Please modify your prompt and retry. 
                To learn more about our content filtering policies please read our documentation: 
                https://go.microsoft.com/fwlink/?linkid=2198766
                """
                ).send()
```

In above code, we get the `conversational_rag_chain` from the `cl.user_session` and invoke it with the user's message. We set the `session_id` in the `config` parameter to get the chat history for each session. We then send the response to the user in chunks of 20 characters.

The following code is simulating the stream response in the UI. 

``` python
    stream_size = 20
    for i in range(int(round(len(response) / stream_size)) + 1):
        msg.content = response[0 : (i + 1) * stream_size]
        await msg.send()
        time.sleep(0.05)
```

The AzureOpenAI has content limitation, the user input which pass to the OpenAI API will be filtered if it contains any offensive or inappropriate content. So, we need to handle this situation. We can use the `ErrorMessage` class to send the error message to the user if the response is filtered.

## Chat Demo Screenshoots
Below are screenshots of the chatbot conversation UI and response.

{% image /assets/images/ai-ml/chainlit-chatbot-chat-1.png %}

{% image /assets/images/ai-ml/chainlit-chatbot-chat-2.png %}

{% image /assets/images/ai-ml/chainlit-chatbot-chat-3.png %}

## Conclusion
In this blog, we have built a RAG chatbot using the `LangChain`, `Chainlit` and `OpenAI` base on previous `FAISS` index. We have used the `Chainlit` to build the chatbot UI, and the `LangChain` to build the conversation chain. We have also used the `FAISS` library to load the `FAISS` vector index and the `AzureChatOpenAI` language model. Finally, we have used the `RunnableWithMessageHistory` class to handle the chat history and the `ErrorMessage` class to handle the content filter.

If you want to get the full code, you can find it in the [GitHub repository](https://github.com/stonefishy/converstional-rag-chatbot). In this demo, we just put the 4 PDF documents which upgrade Wix3, Wix4 to Wix5. You can replace it with your own documents.