# Nutrition RAG Assistant

A retrieval-augmented chatbot that answers everyday nutrition questions using evidence from PDFs, text files, and YouTube video transcripts. The system combines semantic search over a custom vector database with a large language model to generate grounded, transparent answers.

---

## Project Overview

This project implements an end-to-end Retrieval Augmented Generation (RAG) pipeline in the nutrition domain. Users can ask practical nutrition questions and receive responses that are explicitly grounded in retrieved source documents rather than free-form model knowledge.

The application is designed to demonstrate:
- Document ingestion and semantic indexing
- Vector similarity search using DuckDB
- Tool-augmented LLM reasoning with CrewAI
- A user-facing Streamlit chat interface with source transparency

Example questions include:
- “Are zero-sugar electrolyte drinks okay to drink every day?”
- “How much protein should a college student eat in a day?”
- “What foods support gut health?”

---

## Domain Overview and Problem Statement

Nutrition information online is widespread but often fragmented, contradictory, or poorly sourced. Students and non-experts frequently encounter advice driven by trends or marketing rather than evidence, making it difficult to identify reliable guidance.

This project addresses that problem by:
- Centralizing nutrition information from reputable sources
- Enabling semantic search across long-form documents
- Forcing the language model to ground answers in retrieved text
- Displaying similarity scores so users can see why information was selected

The goal is not medical advice, but clear, evidence-based explanations for common nutrition questions.

---

## System Architecture and RAG Pipeline

The project consists of two main phases: **offline indexing** and **online querying**.

### Offline Indexing (Document Processing)

Documents are processed in a separate indexing pipeline to build a persistent vector database.

Steps:
1. **Document Loading**
   - PDFs, text files, HTML, and YouTube transcripts are loaded from a Google Drive folder using `docling`.
2. **Chunking**
   - Documents are split into semantically meaningful chunks using a `HybridChunker` with a Hugging Face tokenizer.
3. **Embeddings**
   - Each chunk is embedded using `sentence-transformers/all-MiniLM-L6-v2`.
4. **Vector Storage**
   - Embeddings, text, and metadata are stored in a DuckDB database with the Vector Similarity Search (VSS) extension.

Output:
- A DuckDB vector store (`nutrition_vector.duckdb`) containing all indexed nutrition content.

### Online Querying (Application Runtime)

The interactive application is built in Python and runs via Streamlit.

Query flow:
1. User submits a question through the Streamlit chat UI.
2. The query is embedded and compared against stored vectors using DuckDB VSS.
3. Top-N similar document chunks and similarity scores are retrieved.
4. A CrewAI agent synthesizes an answer using the retrieved passages.
5. Streamlit displays:
   - The generated answer
   - Retrieved passages
   - Similarity scores for transparency

This separation ensures efficient retrieval while keeping the LLM focused on reasoning and explanation.

---

## Document Collection Summary

The nutrition corpus is a curated set of educational and evidence-oriented materials.

### Source Types
- **PDFs**
  - USDA and WIC nutrition education materials
  - Dietary Guidelines for Americans (2020–2025)
  - Topic-specific nutrition fact sheets (hydration, sugar intake, gut health)
- **Text and HTML files**
  - Cleaned educational articles on common nutrition topics
- **YouTube Transcripts**
  - Transcripts from reputable nutrition and health education channels

### Rationale
- Sources emphasize evidence-based guidance over trends
- Content reflects real questions asked by college students
- Mixed document formats test robustness of the RAG pipeline

After chunking, the corpus contains over **1,300 semantic chunks**, enabling meaningful retrieval without excessive noise.

---

## Agent Configuration and Rationale

The conversational logic is implemented using a CrewAI agent.

### Agent Definition

```python
role = "Nutrition Content Assistant"
goal = "Answer questions about nutrition using evidence from the database"
backstory = (
    "You are an expert with access to a curated nutrition database. "
    "You provide clear, evidence-based explanations, avoid medical diagnoses, "
    "and ground responses in retrieved passages whenever possible."
)

### Task Configuration

```python
description = (
    "Answer the following nutrition-related question using evidence from the "
    "nutrition database. Provide a clear, accurate explanation and cite the "
    "retrieved passages."
)

expected_output = (
    "A structured answer that summarizes key nutrition facts, "
    "uses retrieved passages as evidence, and provides a simple, "
    "actionable recommendation."
)

### Design Rationale

- The role and goal constrain the agent to evidence-based explanations
- The backstory discourages hallucination and medical claims
- Task configuration enforces structure and grounding
- Similarity scores and source text are exposed to users for transparency


## Project Structure

.
├── app.py                     # Streamlit chat interface
├── backend/
│   ├── agent.py               # RAG agent, tools, and task configuration
│   ├── database.py            # DuckDB vector search wrapper
│   └── nutrition_vector.duckdb# Vector database built during indexing
├── config.py                  # Configuration settings
├── requirements.txt           # Python dependencies
├── README.md                  # Project documentation


##Installation and Setup
#Prerequisites

-Python 3.10+
-OpenAI API key

#Setup
git clone https://github.com/your-username/tonos-lpp-rag.git
cd tonos-lpp-rag
pip install -r requirements.txt

Set your OpenAI API key: 

export OPENAI_API_KEY="your_api_key_here"

On Windows PowerShell:

setx OPENAI_API_KEY "your_api_key_here"

##Running the Application
streamlit run app.py

##Streamlit Deployment
The application can be deployed using Streamlit Community Cloud.

Deployment link:
