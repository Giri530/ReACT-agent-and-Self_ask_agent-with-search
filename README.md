ReAct Agent & Self-Ask Agent with Search:

This project demonstrates how to build intelligent, tool-using agents with LangChain and OpenAI's GPT models, using two key techniques:
ReAct Agent: Combines reasoning (thought) and action (tool usage) for dynamic question answering.
Self-Ask Agent with Search: Breaks complex questions into sub-questions and uses external search to answer them step-by-step.

🔧 Technologies Used
Python
OpenAI GPT-4o
LangChain
Google Serper API
Tavily Search API

Setup Instructions
Install dependencies:
!pip install langchain langchain_community openai

Set up API keys (can be stored securely using google.colab.userdata):
import os
from google.colab import userdata
os.environ["OPENAI_API_KEY"] = userdata.get('OPENAI_API_KEY')
os.environ["SERPER_API_KEY"] = userdata.get('SERPER_API_KEY')
os.environ["TAVILY_API_KEY"] = userdata.get('TAVILY_API_KEY')

ReAct Agent
          ->Uses create_react_agent() from LangChain
          ->Integrates Google Search via GoogleSerperAPIWrapper
          ->Follows a structured prompt to think, act, and reason iteratively
agent_executor.invoke({"input": "Who is the current Prime Minister of India?"})
# Output: Narendra Modi

Self-Ask Agent with Search
            ->Uses create_self_ask_with_search_agent()
            ->Breaks down questions and queries search as needed
            ->Great for multi-hop reasoning
agent_executor.invoke({"input": "Which Asian country has the largest population?"})
# Output: China



