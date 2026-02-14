# 🧠 ReAct Agent & Self-Ask Agent with Search

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.8+-blue?style=for-the-badge&logo=python" />
  <img src="https://img.shields.io/badge/OpenAI-GPT--4o-412991?style=for-the-badge&logo=openai" />
  <img src="https://img.shields.io/badge/LangChain-Framework-1C3C3C?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Google%20Serper-Search-4285F4?style=for-the-badge&logo=google" />
  <img src="https://img.shields.io/badge/Tavily-Search%20API-orange?style=for-the-badge" />
</p>

<p align="center">
  A hands-on implementation of two powerful LLM agent paradigms —<br/>
  <strong>ReAct</strong> (Reasoning + Acting) and <strong>Self-Ask with Search</strong> —<br/>
  using LangChain and OpenAI GPT-4o for intelligent, tool-augmented question answering.
</p>

---

## 📌 Table of Contents

- [Overview](#-overview)
- [Agent Types](#-agent-types)
- [Tech Stack](#-tech-stack)
- [How It Works](#-how-it-works)
- [Setup & Installation](#-setup--installation)
- [Usage Examples](#-usage-examples)
- [When to Use Which Agent](#-when-to-use-which-agent)
- [Project Structure](#-project-structure)
- [Contributing](#-contributing)

---

## 🔍 Overview

This project demonstrates how to build **intelligent, tool-using agents** with LangChain and OpenAI GPT-4o using two foundational agentic techniques:

| Agent | Core Idea |
|-------|-----------|
| 🔁 **ReAct Agent** | Alternates between **Reasoning** (thought) and **Acting** (tool use) in a loop until it reaches an answer |
| 🔎 **Self-Ask Agent** | Breaks complex questions into **sub-questions**, searches for each, and combines results step-by-step |

Both agents are capable of browsing the web in real-time to answer questions that require **up-to-date or multi-hop information** — something a plain LLM cannot do alone.

---

## 🤖 Agent Types

### 1 — ReAct Agent (Reasoning + Acting)

The ReAct agent follows a structured **Thought → Action → Observation** loop:

```
User Question
      │
      ▼
  [Thought]  ← LLM reasons about what to do next
      │
      ▼
  [Action]   ← Calls a tool (e.g., Google Search)
      │
      ▼
[Observation] ← Gets the search result
      │
      ▼
  [Thought]  ← Reasons again with new info
      │
      ▼
 [Final Answer] ← Returns answer to user
```

**How it works:**
- Uses `create_react_agent()` from LangChain
- Integrates **Google Search** via `GoogleSerperAPIWrapper`
- Follows a structured ReAct prompt to think, act, and iterate
- Excellent for **factual, real-time queries** that need a single verified answer

**Example:**
```python
agent_executor.invoke({"input": "Who is the current Prime Minister of India?"})
# Thought: I need to search for the current PM of India.
# Action: Google Search → "current Prime Minister of India"
# Observation: Narendra Modi is the PM since 2014...
# Final Answer: Narendra Modi
```

---

### 2 — Self-Ask Agent with Search

The Self-Ask agent **decomposes** a complex question into smaller follow-up questions and resolves each one:

```
Complex Question
      │
      ▼
 "Do I need to follow up?" → YES
      │
      ▼
 Follow-up Q1  →  Search  →  Answer 1
      │
      ▼
 Follow-up Q2  →  Search  →  Answer 2
      │
      ▼
 Combine Answers
      │
      ▼
 Final Answer
```

**How it works:**
- Uses `create_self_ask_with_search_agent()` from LangChain
- Powered by **Tavily Search API** for high-quality web results
- Ideal for **multi-hop reasoning** — questions that require chaining multiple facts together
- The agent keeps asking sub-questions until enough context is gathered

**Example:**
```python
agent_executor.invoke({"input": "Which Asian country has the largest population?"})
# Follow up: What are the most populous countries in Asia?
# Intermediate answer: China, India, Indonesia...
# Follow up: What is China's population?
# Intermediate answer: ~1.4 billion
# Final Answer: China
```

---

## 🛠️ Tech Stack

| Tool | Purpose |
|------|---------|
| **Python** | Core programming language |
| **OpenAI GPT-4o** | LLM backbone for reasoning and response generation |
| **LangChain** | Agent framework — chains, tools, and prompt management |
| **Google Serper API** | Real-time Google Search for ReAct agent |
| **Tavily Search API** | High-quality web search for Self-Ask agent |
| **Google Colab** | Recommended runtime with secure key management |

---

## 🔄 How It Works

```
┌──────────────────────────────────────────────────┐
│                  User Question                    │
└──────────────────────┬───────────────────────────┘
                       │
                       ▼
          ┌────────────────────────┐
          │    LangChain Agent     │
          │  (GPT-4o as backbone)  │
          └────────┬───────────────┘
                   │
       ┌───────────┴───────────┐
       │                       │
       ▼                       ▼
 ReAct Agent           Self-Ask Agent
 (single-hop)          (multi-hop)
       │                       │
       ▼                       ▼
 Google Serper          Tavily Search
 (real-time web)        (structured web)
       │                       │
       └───────────┬───────────┘
                   │
                   ▼
            Final Answer
```

---

## 🚀 Setup & Installation

### 1. Install Dependencies

```bash
pip install langchain langchain_community openai
```

Or install all at once:

```bash
pip install langchain langchain_community openai google-search-results tavily-python
```

### 2. Set Up API Keys

#### In Google Colab (Recommended)

```python
import os
from google.colab import userdata

os.environ["OPENAI_API_KEY"] = userdata.get('OPENAI_API_KEY')
os.environ["SERPER_API_KEY"] = userdata.get('SERPER_API_KEY')
os.environ["TAVILY_API_KEY"] = userdata.get('TAVILY_API_KEY')
```

#### Locally via `.env` file

```env
OPENAI_API_KEY=your_openai_key_here
SERPER_API_KEY=your_serper_key_here
TAVILY_API_KEY=your_tavily_key_here
```

```python
from dotenv import load_dotenv
load_dotenv()
```

> 🔑 Get your API keys:
> - OpenAI: https://platform.openai.com/api-keys
> - Serper: https://serper.dev
> - Tavily: https://tavily.com

---

## 💡 Usage Examples

### ReAct Agent

```python
from langchain.agents import create_react_agent, AgentExecutor
from langchain_community.utilities import GoogleSerperAPIWrapper
from langchain.tools import Tool
from langchain_openai import ChatOpenAI

# Initialize LLM
llm = ChatOpenAI(model="gpt-4o", temperature=0)

# Set up search tool
search = GoogleSerperAPIWrapper()
tools = [
    Tool(
        name="Search",
        func=search.run,
        description="Useful for answering questions about current events or facts."
    )
]

# Create and run agent
agent = create_react_agent(llm, tools, prompt)
agent_executor = AgentExecutor(agent=agent, tools=tools, verbose=True)

agent_executor.invoke({"input": "Who is the current Prime Minister of India?"})
# Final Answer: Narendra Modi
```

### Self-Ask Agent

```python
from langchain.agents import create_self_ask_with_search_agent, AgentExecutor
from langchain_community.tools.tavily_search import TavilySearchResults
from langchain_openai import ChatOpenAI

# Initialize LLM
llm = ChatOpenAI(model="gpt-4o", temperature=0)

# Set up Tavily search
tools = [TavilySearchResults(max_results=3)]

# Create and run agent
agent = create_self_ask_with_search_agent(llm, tools, prompt)
agent_executor = AgentExecutor(agent=agent, tools=tools, verbose=True)

agent_executor.invoke({"input": "Which Asian country has the largest population?"})
# Final Answer: China
```

---

## 🎯 When to Use Which Agent

| Scenario | Best Agent |
|----------|-----------|
| Single factual question needing web search | ✅ ReAct Agent |
| Question requiring multiple chained facts | ✅ Self-Ask Agent |
| "Who won X award in Y year?" | ReAct |
| "What is the capital of the country with the largest GDP in Asia?" | Self-Ask |
| Real-time news or current events | ReAct |
| Comparative or multi-step reasoning | Self-Ask |

---

## 📁 Project Structure

```
react-selfask-agents/
├── react_agent.py          # ReAct agent implementation
├── self_ask_agent.py       # Self-Ask agent implementation
├── requirements.txt        # Python dependencies
└── README.md               # This file
```

> 💡 Can also be run as a single Jupyter / Colab notebook.

---

## 🤝 Contributing

```bash
git clone https://github.com/Giri530/react-selfask-agents.git
git checkout -b feature/your-feature
git commit -m "Add your feature"
git push origin feature/your-feature
# Open a Pull Request
```

---

<p align="center">
  <strong>Made with ❤️ by Girinath &nbsp;·&nbsp; Powered by LangChain & GPT-4o</strong>
</p>
