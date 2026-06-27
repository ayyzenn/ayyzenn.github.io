---
title: "YouTube Summarizer: What I Built with LangGraph and Gemini"
date: 2025-06-06
tags: ["ai", "langgraph", "gemini", "python", "youtube"]
---
## What I Built

I wanted to paste a YouTube URL and get a summary — or ask questions about the video — without watching the whole thing. This project uses **LangGraph** for the workflow and **Gemini** for the actual summarizing and Q&A.

---

## Key Features of Agentic Workflows

- **Modularity** — each step can be built and tested on its own
- **State** — data flows from step to step in a structured way
- **Reusability** — the same steps can be reused in other projects
- **Autonomy** — each step can decide what to do based on the input it receives

---

## Popular Tools for Agentic Workflows

| Tool          | Description                                                             | Language | Best For                              |
| ------------- | ----------------------------------------------------------------------- | -------- | ------------------------------------- |
| **LangGraph** | Graph-based AI workflow framework with state management.                | Python   | Custom step-by-step pipelines         |
| **CrewAI**    | Agent orchestration tool inspired by human teams and role delegation.   | Python   | Role-based agent collaboration        |
| **Autogen**   | Microsoft's multi-agent framework for goal-oriented dialogue and tasks. | Python   | Conversational multi-agent systems    |
| **LangChain** | General framework for chaining LLMs with tools, memory, and logic.      | Python   | Broader LLM apps beyond just agents   |
| **AgentOps**  | Infra layer for deploying and monitoring agentic systems.               | Platform | Scaling, testing, and managing agents |

---

## Why I Chose LangGraph

For this project, I chose **LangGraph** because:

- It gives **clear control** over the order of steps
- It fits **pipelines** like: get YouTube URL → fetch transcript → summarize → answer questions
- It supports **state**, so each step can use output from the previous one

## YouTube Video Q&A Agent

This project uses one agent that does two things:

1. **Summarize** the video transcript
2. **Answer questions** about the video content

### Responsibilities

- Extract the transcript from a YouTube video
- If the user asks for a summary, generate a short summary
- If the user asks a question, answer it using the transcript as context
- Use Google's **gemini-2.0-flash** model through `langchain-google-genai`

---

### How It Works

1. **Transcript extraction**  
   The agent fetches the transcript with `youtube-transcript-api` and cleans the text.

2. **Routing based on user input**  
   - If the user types `"summary"`, the full transcript is sent to Gemini with a summary prompt
   - If the user asks a question, both the transcript and the question are sent to Gemini

3. **Response**  
   Gemini returns either a summary or an answer, which is shown to the user.

---

### Technologies Used

- `langchain-google-genai` — connects to Gemini
- `youtube-transcript-api` — fetches video transcripts
- `LangGraph` — runs the workflow
- `dotenv` — loads the API key from a `.env` file

---

### Example Workflow

```plaintext
User enters YouTube URL → Transcript fetched →
User enters "summary" → Gemini generates a summary
OR
User enters a question → Gemini answers using the transcript
```

---

### Source Code

Full project on GitHub:

https://github.com/ayyzenn/youtube-video-qa.git
