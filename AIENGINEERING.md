# AI Engineering

My summary notes from an AI engineering course.

## Table of Contents

- [Prompt Engineering](#prompt-engineering)
- [Context Window and Tokens](#context-window-and-tokens)
- [Embedding](#embedding)
- [LangChain](#langchain)
- [LLM vs. Agent](#llm-vs-agent)
- [Vector DB](#vector-db)
- [RAG](#rag)
- [LangGraph](#langgraph)
- [MCP](#mcp)

---

## Prompt Engineering

When you ask an AI a question, the prompt goes directly to a Large Language Model (LLM) such as ChatGPT. It works like a chat with the AI through a prompt window.

Prompt engineering is the practice of writing prompts, including the **system prompt** of an agent, so the model gives good results. You compare the answers you get from different prompts and keep the ones that give the best quality.

Main techniques:

| Technique | Description |
|---|---|
| **Zero-shot** | Ask the AI to do a task without giving any example. |
| **One-shot** | Like zero-shot, but you provide one example, e.g. "do it like this template". |
| **Few-shot** | Provide several examples for answering a specific kind of question. |
| **Chain of thought** | Give the model a sequence of steps to reason through to solve a specific problem. |

## Context Window and Tokens

Models like Claude, Gemini and GPT are **Transformers** trained on large datasets. Text is split into **tokens** (small chunks of text, such as words or parts of words). The **context window** is the maximum number of tokens a model can hold in its "short-term memory" at once (your prompt, the conversation, and the answer).

**Analogy:** think of people memorizing the digits of pi (3.14159...). The person who can memorize more digits has a bigger "window". Models differ in the same way.

The LLM focuses on the parts of the context that are relevant to the question and ignores irrelevant information (for example, a story about Bob and Belly with apples, green and red: only the details related to the question matter).

**Example:** Gemini 2.5 Pro has a context window of about 1M tokens, which can hold a large number of files. But if we want it to read a 500 GB database, it cannot read everything at once. That is why we use **embeddings**.

## Embedding

Embedding changes how we represent information. Instead of reading text as words, we convert its **meaning** into numbers (vectors). Related concepts end up close to each other, for example animal and dog, pen and paper, laptop and cellphone. **The closer the numbers, the closer the meaning.**

To build a chatbot that reads and answers questions about a company's own information, we need a tool that can:

- Save conversation history
- Learn the company knowledge base
- Handle multi-step interactions

That is why we use **LangChain**.

## LangChain

LangChain gives an application the tools to read, memorize and answer users' questions **without depending on a specific model SDK** such as OpenAI's. If we change the model, the structure of answering and gathering information stays the same.

A LangChain application typically uses:

- An LLM
- A memory saver
- Embeddings (text to vectors)
- A vector DB such as **Chroma** to store the vectors
- Semantic search

## LLM vs. Agent

- **LLM:** a static brain. It answers questions based on its training data.
- **Agent:** has autonomy, memory and tools, so it can perform the tasks needed to complete your request.

## Vector DB

A vector DB stores data **by meaning, not by exact value**. For example, company policies are converted into embeddings and stored in the vector DB (e.g. **Chroma**).

Key concepts for storing and retrieving data:

- **Embeddings:** store data as numbers that represent its meaning.
- **Dimensionality:** a word can have multiple meanings. Each is represented as numbers in a multi-dimensional space, and vectors that are close to each other are fetched together.
- **Retrieval:** getting the right data back. It relies on **similarity scoring** and **chunking with overlap**. Chunking means splitting large data into smaller pieces so it is easier to retrieve, and overlap keeps context between neighbouring chunks.

## RAG

**Retrieval-Augmented Generation (RAG)** lets the LLM search for data directly in the vector DB before answering.

- **Retrieval:** uses semantic search, which searches by meaning rather than exact keywords. The question is also embedded, so matching happens meaning to meaning.
- **Augmented:** the LLM is given specific information from the vector DB instead of relying only on its static training knowledge. It also lets the company add policies that control how the LLM behaves when answering.
- **Generation:** the LLM reads the retrieved data and generates the answer from it.

Every RAG system needs its own strategy, because the way the data can be chunked differs.

> LangChain is limited for multi-task and multi-connection workflows. For chatbot applications it may be enough, but for complex chains we need **LangGraph**.

## LangGraph

Imagine asking: *"How does the company handle data privacy for EU customers?"* This requires several chains, for example checking GDPR, local regulations and company standards. With plain chains we would need many `if` statements to control the flow.

LangGraph is built from:

- **Nodes:** individual units of computation. In the example: search, extract and clean info, evaluate, cross-reference, identify, generate.
- **Edges:** connect the nodes and define the execution flow.
- **State:** shared between all nodes and stored in a `StateGraph` (a class that holds the information of the graph).

Example of quality control: each step's result gets a score. If it is above a threshold (e.g. 75%), the flow moves to the next node. If not, it goes back to the previous node to gather more information.

LangGraph supports:

- Loops between nodes
- Conditional branching
- Persistent state

## MCP

**Model Context Protocol (MCP)** is a standard created by Anthropic.

**Problem:** when building a chatbot, we connect the AI to an internal DB. Connecting to an external system to fetch information takes time and custom work.

**Idea:** MCP is like an API, but designed for AI agents. A normal API requires the developer to know the endpoints, URLs and the kind of data each system supports. MCP provides **self-describing interfaces** that an AI agent can understand and use on its own. This moves the integration effort from the developer to the agent.

The community has already written MCP servers for GitHub, SQL databases and more. You can use them directly in your agent without writing any code.
