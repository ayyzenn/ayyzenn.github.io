---
title: "Building a YouTube Transcript Summarizer with LangGraph and Gemini"
date: 2025-06-06
description: "A walkthrough of how I built an AI-powered summarizer for YouTube videos using LangGraph and Gemini API."
tags: ["AI", "LangGraph", "Gemini", "Python", "YouTube"]
---

## 🧠 What is an Agentic Workflow?

An **Agentic Workflow** is a system where multiple autonomous AI agents work together to solve complex tasks. Each agent is responsible for a specific role (such as fetching data, summarizing text, or answering questions), and they collaborate in a structured flow to complete the overall goal.

This workflow design mimics how human teams operate — with clear roles, communication, and coordination.

---

## 🧱 Key Features of Agentic Workflows

- **Modularity**: Each agent can be independently developed and reused.
- **Statefulness**: Agents maintain and pass structured data (state) across steps.
- **Reusability**: Agents can be composed into different workflows.
- **Autonomy**: Agents can make decisions based on input and context.

---

## 🛠️ Popular Tools for Agentic Workflows

| Tool          | Description                                                             | Language | Best For                              |
| ------------- | ----------------------------------------------------------------------- | -------- | ------------------------------------- |
| **LangGraph** | Graph-based AI workflow framework with state management.                | Python   | Customizable and flexible workflows   |
| **CrewAI**    | Agent orchestration tool inspired by human teams and role delegation.   | Python   | Role-based agent collaboration        |
| **Autogen**   | Microsoft's multi-agent framework for goal-oriented dialogue and tasks. | Python   | Conversational multi-agent systems    |
| **LangChain** | General framework for chaining LLMs with tools, memory, and logic.      | Python   | Broader LLM apps beyond just agents   |
| **AgentOps**  | Infra layer for deploying and monitoring agentic systems.               | Platform | Scaling, testing, and managing agents |

---

## ⚙️ Why I Chose LangGraph

For this project, I chose **LangGraph** because:

- It offers **graph-based control** over the workflow between agents.
- It’s ideal for **step-by-step pipelines**, like:
  - 👉 Get YouTube URL → Fetch transcript → Summarize → Answer questions.
- It natively supports **state management**, so agents can build on each other’s output.

## 🤖 Agent: YouTube Video Q&A Agent

This project uses a single, multifunctional agent to handle two core tasks:
1. **Summarizing the video transcript**
2. **Answering user questions** based on the video content

### 🧩 Responsibilities
- Extract the transcript from a YouTube video.
- Based on user input, either:
  - Generate a summary of the content, or
  - Answer a natural language question related to the video.
- Use Google's **gemini-2.0-flash** model via `langchain-google-genai` to handle both tasks.

---

### ⚙️ How It Works

1. **Transcript Extraction**  
   The agent first retrieves the transcript using the `youtube-transcript-api`. It parses the text into a clean format suitable for processing.

2. **Routing Based on User Input**  
   The agent inspects the user prompt:
   - If the user types `"summary"`, it sends the full transcript to Gemini with a prompt asking for a concise summary.
   - If the user enters a specific question, it sends both the transcript and the question to Gemini, asking for an accurate, context-aware answer.

3. **Response Generation**  
   Gemini returns either a summary or a direct answer, which is then displayed to the user.

---

### 🛠️ Technologies Used

- `langchain-google-genai` to integrate Gemini Pro for LLM responses.
- `youtube-transcript-api` to fetch video transcripts.
- `LangGraph` to manage and run the workflow logic.
- `dotenv` for secure API key handling.

---

### 🔄 Example Workflow

```plaintext
User enters YouTube URL → Transcript fetched → 
User enters "summary" → Gemini generates a summary
OR
User enters a question → Gemini answers using the transcript context
```
--- 

### 📂 Check Out the Code
You can find the full source code for this project on GitHub:

https://github.com/ayyzenn/youtube-video-qa.git

