---
title: Leverage AI Tools to write code base on Jira ticket requirements.
date: 2025-09-12 16:03:15
categories: AI/ML
tags: [AI, MCP, Github Copilot]
swiper: true
top: true
swiperImg: "/medias/bg-images/v2-b44931ea513e5f7b3b06f2703f79653c_1440w.webp"
---

Have you ever imagined if AI can help you write code based on Jira ticket requirements? The AI technology is advancing rapidly, and there are now tools available that can assist developers in generating code snippets, functions, or even entire modules by interpreting the requirements specified in Jira tickets.

In this blog, we will explore how to leverage AI tools to automatically generate code by following the specifications outlined in Jira tickets.

## Set up Atlassian MCP
To get started, we need a `MCP` (Model Context Protocol) tool to interact with Jira. Fortunately, there is an open-source `mcp-atlassian` project available on GitHub that provides the necessary functionality to interact with Jira and extract ticket information. The github project link is https://github.com/sooperset/mcp-atlassian. It supports `Cloud` and `Data Center` and `Server` versions of Jira.

My company JIRA server is `Data Center` version, so I follow the `Data Center` setup instructions to set up the MCP server. 

First, you need to create your personal tokens for jira and confluence. And make sure you already installed `docker` on your local environment. Open the universal editor `Visual Studio Code`, add the `mcp-atlassian` mcp server configuration in `mcp.json` file. like below:

``` json
{
  "mcpServers": {
    "mcp-atlassian": {
      "command": "docker",
      "args": [
        "run",
        "--rm",
        "-i",
        "-e", "CONFLUENCE_URL",
        "-e", "CONFLUENCE_PERSONAL_TOKEN",
        "-e", "CONFLUENCE_SSL_VERIFY",
        "-e", "JIRA_URL",
        "-e", "JIRA_PERSONAL_TOKEN",
        "-e", "JIRA_SSL_VERIFY",
        "ghcr.io/sooperset/mcp-atlassian:latest"
      ],
      "env": {
        "CONFLUENCE_URL": "https://confluence.your-company.com",
        "CONFLUENCE_PERSONAL_TOKEN": "your_confluence_pat",
        "CONFLUENCE_SSL_VERIFY": "false",
        "JIRA_URL": "https://jira.your-company.com",
        "JIRA_PERSONAL_TOKEN": "your_jira_pat",
        "JIRA_SSL_VERIFY": "false"
      }
    }
  }
}
```

Please update above environment variables with your own values. Below is my configuration.

{% image /assets/images/ai-ml/atlassian-mcp.png, width="600px", alt="Atlassian MCP Configuration"%}

 If you don't want the confluence integration, you can remove the related environment variables.

## Let's play with it
Once done, Open the `Github Copilot` panel in `Visual Studio Code`. Let's ask AI to return us the requirements of a Jira ticket. Below is an example of a Jira ticket.

{% image /assets/images/ai-ml/atlassian-jira-example.png, width="900px", alt="Jira ticket Example"%}.

Let's ask AI to return the requirements of this ticket. Below is the prompt I used.

```
what's the description of the jira ticket HOP-35704
```

And below is the result returned by AI.

{% image /assets/images/ai-ml/atlassian-jira-ai-response.png, width="900px", alt="AI return Jira requirements"%}

From above screenshot, we can see AI can return the ticket description correctly via the tool `jira_get_issue` of the `mcp-atlassian` mcp server.

We can also ask AI to write code based on the ticket requirements. Let's keep using the same ticket as an example. 

```
update the code base on jira ticket HOP-35704
```

{% image /assets/images/ai-ml/atlassian-jira-ai-code-1.png, width="900px" %}
{% image /assets/images/ai-ml/atlassian-jira-ai-code-2.png, width="900px", alt="AI Coding base on JIRA requirements"%}

From the screenshot, we can see the AI can understand the ticket requirements and search the related code file in the project and updat the code. In above screenshot, since I already use AI to update the code and commit it. So you can see the AI is checking each requirements carefully and make sure the code is updated correctly.

## Summary
Leveraging AI tools to write code based on Jira ticket requirements can significantly enhance productivity and streamline the development process. By integrating tools like `mcp-atlassian` with AI models, developers can automate the generation of code snippets and functions, allowing them to focus on more complex tasks. This approach not only saves time but also reduces the chances of human error, leading to more efficient and reliable software development.


