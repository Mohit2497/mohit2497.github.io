---
layout: post
title: "Building an AI Agent: A Learning Journey"
date: 2025-09-25
categories: [AI-Machine-Learning, Projects]
tags: [AI-Agents]
image:
  path: /assets/img/gifs/repo_assistant.gif
  alt:
---

This project documents my hands-on experience following the **AI Agent Bootcamp** by [DataTalksClub](https://datatalks.club/).  
My goal was to build a personal AI assistant capable of understanding and answering questions about any GitHub repository — starting from raw data ingestion all the way to a fully deployed conversational web app.

This blog captures the process, lessons learned, and technical milestones from each stage of the bootcamp.

---

## Step 1: Ingesting the Data

Every intelligent system begins with reliable, structured data.  
Before my AI agent could answer questions, it needed a solid ingestion pipeline to extract documentation and code from GitHub repositories.

All the development work for this step is located in the `/project` directory.

### Key Insights from Day 1

**1. Efficient Repository Downloading**  
Instead of using `git clone`, I learned a faster, cleaner approach — downloading repositories as `.zip` archives directly via Python.  
Using the `requests`, `io`, and `zipfile` libraries, I was able to extract repositories entirely in memory, avoiding temporary files and speeding up the process.

**2. Frontmatter for Metadata Extraction**  
The `python-frontmatter` library was a revelation. It separates structured metadata (like titles and tags) from document content.  
This is invaluable for creating searchable, well-organized datasets — especially when preparing a knowledge base for a Retrieval-Augmented Generation (RAG) system.

### From Theory to Practice

Using these techniques, I built a data ingestion pipeline for the `microsoft/ai-agents-for-beginners` repository.  
The pipeline successfully extracted documentation and metadata, and it can now be reused for any public GitHub repository.

---

## Step 2: Chunking Large Documents

Once the data was ingested, I faced a new challenge — the size of the documents.  
Large files cannot be directly processed by a language model, so I implemented **document chunking**, breaking content into smaller, meaningful sections.

### Key Insights from Day 2

**1. Sliding Window for Context-Aware Chunking**  
Rather than cutting text arbitrarily, I used an overlapping sliding window technique to preserve context between chunks — ensuring continuity and semantic consistency.

**2. Section-Based Chunking for Structured Documents**  
Many Markdown documents have headers and sections. By splitting based on these structures, I created topic-focused chunks that aligned naturally with document flow.

**3. Intelligent Chunking with LLMs**  
For complex, unstructured documents, I experimented with prompting a Large Language Model (LLM) to perform chunking.  
Although more resource-intensive, this approach yielded highly coherent results.

### From Theory to Practice

In my `/project` notebook, I tested all three chunking approaches.  
For the `microsoft/ai-agents-for-beginners` repository, the **sliding window** method provided the best balance between simplicity and contextual quality.

---

## Step 3: Adding Search

After chunking, I needed a way to retrieve relevant data.  
I implemented a search system combining both **keyword-based** and **semantic** techniques.

### Key Insights from Day 3

**1. Text Search for Keyword Matching**  
Using the `minsearch` library, I implemented a fast lexical search engine.  
It builds an inverted index to quickly locate documents containing specific query terms.

**2. Vector Search for Semantic Meaning**  
To capture the *intent* behind queries, I implemented vector search using the `multi-qa-distilbert-cos-v1` model.  
By converting text into numerical embeddings, the system could find semantically similar documents.

**3. Hybrid Search for Robust Results**  
The best results came from combining both approaches — text search for precision and vector search for context.  
The hybrid search merged both result sets, removing duplicates for comprehensive retrieval.

### From Theory to Practice

I indexed over 12,500 chunks from the Microsoft AI repository, creating both text and vector search indices.  
The final system was saved as `microsoft_search_system.pkl` for later use by the conversational agent.

---

## Step 4: Building the Conversational Agent

With the ingestion, chunking, and search systems ready, I built the **conversational AI agent** that ties everything together.  
This agent understands user queries, searches for relevant data, and synthesizes informative answers.

### Key Insights from Day 4

**1. An Agent is an LLM with Tools**  
What makes an "agent" different from a chatbot is its ability to use external tools — in this case, the search functions I built.

**2. Function Calling for Dynamic Actions**  
Modern LLMs can now "call" functions. When the model detects that a tool is needed, it outputs structured arguments, which are executed by the application before the model generates its response.

**3. Simplifying with Pydantic AI**  
The `pydantic-ai` library automates much of this complexity. It generates tool schemas, manages reasoning loops, and structures responses, keeping the code clean and maintainable.

**4. The System Prompt as the Agent's Constitution**  
I carefully designed a detailed system prompt defining the agent's role, tools, and behavior.  
This ensures the agent remains accurate, consistent, and aligned with its purpose — answering questions about the Microsoft AI repository.

### From Theory to Practice

Using `pydantic-ai` and the `gemini-2.5-flash` model, I created a `Repo Assistant` capable of understanding user questions and retrieving answers through the hybrid search system.

---

## Step 5: Evaluating the Agent

Once the agent worked, I focused on evaluating its performance systematically.

### Key Insights from Day 5

**1. Logging Interactions for Traceability**  
Every interaction — from input prompts to tool calls — was logged as structured JSON.  
This provided full visibility into the agent's reasoning process.

**2. Automated Evaluation with "LLM as a Judge"**  
To scale evaluation, I created an `Evaluation_Agent` that automatically reviews responses and scores them based on quality and relevance.

**3. Structured Feedback with Pydantic**  
Using `Pydantic` models, the evaluation outputs were standardized into JSON objects with fields like `answer_relevant`, `answer_citations`, and `completeness`.

**4. Scalable Testing with AI-Generated Data**  
I built a `Question_Generator` agent that automatically produced realistic test questions, expanding the evaluation dataset.

### From Theory to Practice

The full evaluation pipeline ran end-to-end:

1. Generated test questions from repository data  
2. Logged all agent interactions  
3. Used the `Evaluation_Agent` to assess responses  
4. Analyzed results in Pandas, achieving over **85% accuracy** across evaluation metrics

---

## Step 6: Publishing the Agent

The final step was turning the notebook-based project into a deployable web application using **Streamlit**.

### Key Insights from Day 6

**1. Refactoring for Production**  
The notebook was split into modular Python files:  
- `ingest.py` — data ingestion  
- `search_tools.py` — search implementation  
- `search_agent.py` — defines the agent  
- `logs.py` — manages interaction logs  
- `app.py` — Streamlit front-end

**2. Building an Interactive UI**  
Using Streamlit, I developed a user-friendly interface with a chat window, conversation history, and API usage tracking.  
The UI also supports streaming responses for a smoother experience.

**3. Deploying to the Cloud**  
I deployed the application via Streamlit Cloud, connecting it directly to my GitHub repository for easy versioning and updates.

**4. Optimizing Startup Time**  
To speed up app initialization, I pre-built the search index with a `build_index.py` script and saved it as `ms_ai_agents_index.pkl`.  
This reduced startup time from over a minute to just a few seconds.

### From Theory to Practice

All the components — ingestion, search, and the conversational agent — now work together in a single Streamlit app.  
The result is an accessible, production-ready AI assistant that can answer questions about any GitHub repository.

---

## Conclusion

This project has been an end-to-end journey through building a **Retrieval-Augmented Generation (RAG)** system from scratch:  
- Data ingestion  
- Chunking and indexing  
- Search and retrieval  
- Conversational reasoning  
- Evaluation  
- Deployment  

Each stage reinforced the principles of data engineering, machine learning, and software design.  
The final result is an intelligent, reliable AI agent — and a deeper understanding of what it takes to build one.

---

**Acknowledgments**  
A huge thank you to **Alexey Grigorev** and the **DataTalksClub** team for designing the AI Agent Bootcamp.  
It's an exceptional program that bridges theory and real-world application, and it's been one of my most rewarding learning experiences.
