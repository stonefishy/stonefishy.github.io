---
title: Understanding the Model Context Protocol (MCP)
date: 2025-04-21 10:38:42
categories: AI/ML
tags: [AI, MCP, Chatbot]
---

In the rapidly evolving landscape of artificial intelligence, ensuring the effective and efficient management of model contexts is crucial for the deployment and utilization of AI systems. The model context is refer to the set of information that is necessary for the `AI model` like `DeepSeek`, `Claude`, `ChatGPT`, `Gemini` to make a correct and efficient decision. However, managing the model context is not an easy task, as it requires a combination of technical expertise, domain knowledge, and business intelligence.

One significant protocol that has emerged to address this challenge is the `Model Context Protocol (MCP)`. Let's talk about it in this blog.

## What is `MCP`?
A brief overview of the `Model Context Protocol (MCP)` which introduced by official:

> `The Model Context Protocol (MCP)` is an open protocol that enables seamless integration between LLM applications and external data sources and tools. Whether you're building an AI-powered IDE, enhancing a chat interface, or creating custom AI workflows, MCP provides a standardized way to connect LLMs with the context they need.

> Think of `MCP` like a USB-C port for AI applications. Just as USB-C provides a standardized way to connect your devices to various peripherals and accessories, MCP provides a standardized way to connect AI models to different data sources and tools.

{% image /assets/images/ai-ml/mcp-like-usb.png %}

So it's a protocol that defines a standardized way to connect `LLMs` with the context they need. The `MCP` is designed to address the challenges of managing the model context. It's introduced by `Anthropic` company at 2024, the `Claude Desktop` supports very well. 

## Why MCP Came Out?
Below  explains the Model Context Protocol’s architecture and capabilities, how it solves the inherent challenges of AI integration.

{% image /assets/images/ai-ml/mcp-before-after.png %}

Before the `MCP`, we usually use the `Function calling`, which allows LLMs to invoke predetermined functions based on user requests, is a well-established feature of modern AI models. Sometimes referred to as “tool use,” function calling is not mutually exclusive with MCP; the new protocol simply standardizes how this API feature works. 

Without MCP, when you use a function call directly with an LLM API, you need to:

1. Define model-specific function schemas, which are JSON descriptions of the function, acceptable parameters, and what it returns.
2. Implement handlers (the actual code that executes when a function is called) for those functions.
3. Create different implementations for each model you support.

With MCP, the MCP standardizes this process by:

1. Defining a consistent way to specify tools (functions) across any AI system.
2. Providing a protocol for discovering available tools and executing them.
3. Creating a universal, plug-and-play format where any AI app can use any tool without custom integration code.

You might be familiar with AI apps that use function calling, like Custom GPTs using GPT Actions. A Custom GPT can determine which API call resolves the user's prompt, create the necessary JSON, then make the API call with it. While this allows some purpose-built tooling, it’s bound to OpenAI’s ecosystem. `MCP` brings similar capabilities to any AI application that implements the protocol, regardless of the underlying model vendor.

## MCP Architectures and Core Components
Below is general architecture of `MCP`:

{% image /assets/images/ai-ml/mcp-general-architecture.png, alt="MCP General Architecture" %}

**Host application**: `LLMs` that interact with users and initiate connections. This includes `Claude Desktop`, AI-enhanced IDEs like `Cursor`, and standard web-based LLM chat interfaces.

**MCP client**: Integrated within the host application to handle connections with MCP servers, translating between the host’s requirements and the Model Context Protocol. Clients are built into host applications, like the MCP client inside Claude Desktop.

**MCP server**: Adds context and capabilities, exposing specific functions to AI apps through MCP. Each standalone server typically focuses on a specific integration point, like GitHub for repository access or a PostgreSQL for database operations.

**Data Source**: The source of information or functionality that the MCP server connects to. This could be a database, API, or any other data source that the server can access.
1. **Local Data Sources**: Your computer’s files, databases, and services that MCP servers can securely access
2. **Remote Services**: External systems available over the internet (e.g., through APIs) that MCP servers can connect to

**Transport layer**: The communication mechanism between clients and servers. MCP supports two primary transport methods:
1. **STDIO (Standard Input/Output)**: Mainly local integrations where the server runs in the same environment as the client.
2. **HTTP+SSE (Server-Sent Events)**: Remote connections, with HTTP for client requests and SS for server responses and streaming.

All communication in MCP uses `JSON-RPC 2.0` as the underlying message standard, providing a standardized structure for requests, responses, and notifications.

## How MCP Works
When a user interacts with a host application (an AI app) that supports `MCP`, several processes occur behind the scenes to enable quick and seamless communication between the AI and external systems. Let's take a look the example.

### Protocol handshake
1. **Initial connection**: When an MCP client (like Claude Desktop) starts up, it connects to the configured MCP servers on your device.
2. **Capability discovery**: The client asks each server "What capabilities do you offer?" Each server responds with its available tools, resources, and prompts.
3. **Registration**: The client registers these capabilities, making them available for the AI to use during your conversation.

### An Example
Let's use an example to talk about it. Below is the example general communication flow of `MCP`:

{% image /assets/images/ai-ml/mcp-communication-flow.png, alt="MCP How Works" %}

Let's say you ask Claude, "What's the weather like in San Francisco today?" Here's what happens:

1. **Need recognition**: Claude analyzes your question and recognizes it needs external, real-time information that wasn't in its training data.
2. **Tool or resource selection**: Claude identifies that it needs to use an MCP capability to fulfill your request.
3. **Permission request**: The client displays a permission prompt asking if you want to allow access to the external tool or resource. 
4. **Information exchange**: Once approved, the client sends a request to the appropriate MCP server using the standardized protocol format.
5. **External processing**: The MCP server processes the request, performing whatever action is needed—querying a weather service, reading a file, or accessing a database.
6. **Result return**: The server returns the requested information to the client in a standardized format.
7. **Context integration**: Claude receives this information and incorporates it into its understanding of the conversation.
8. **Response generation**: Claude generates a response that includes the external information, providing you with an answer based on current data.

## MCP Servers
Since the MCP just released at end of the 2024 by Anthropic, but it is growing rapidly. There are many MCP servers available now, and they are categorized into different categories. There are some awsome MCP servers:

- **GitHub**: MCP server for GitHub, which allows you to access repositories and files in your GitHub account.
- **PostgreSQL**: MCP server for PostgreSQL, which allows you to access and manage your PostgreSQL databases.
- **Docker**: MCP server for Docker, which allows you to manage your Docker containers and images.
- **Kubernetes**: MCP server for Kubernetes, which allows you to manage your Kubernetes clusters and resources.
- **Google Cloud Platform**: MCP server for Google Cloud Platform, which allows you to access and manage your Google Cloud resources.
- **Amazon Web Services**: MCP server for Amazon Web Services, which allows you to access and manage your AWS resources.

There is a MCP servers collections link: https://github.com/punkpeye/awesome-mcp-servers, you can find existing MCP servers and their capabilities. Also, you can create your own MCP server by following the MCP server development guide. We will talk about it in the future.
