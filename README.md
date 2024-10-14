# Hog RAGger

This project implements a two-stage pipeline for document summarization and question answering using Retrieval-Augmented Generation (RAG).

## Overview

This repository contains two main Python scripts that work together:

1. `RAG_summarize.ipynb`: Summarizes a corpus of documents using the BART-large-CNN model.
2. `actualpls_RAG.ipynb`: Implements a RAG pipeline for answering questions based on the summarized documents.

The pipeline works as follows:
1. A `corpus.json` file containing original documents is processed by `rag_summarize.py`.
2. Summaries are generated and stored in `summarized.json`.
3. `actualpls_rag.py` then operates on `summarized.json` to answer questions using a RAG approach.

## Tech Stack

- Python 3.x
- PyTorch
- Transformers (Hugging Face)
- LangChain
- Chroma (Vector Store)
- Groq (LLM API)
- Sentence Transformers
- Anvil Uplink

## How to Run

### 1. Document Summarization

1. Ensure you have `corpus.json` in your project directory.
2. Run the summarization script in google colab/locally (needs GPU):
   ```
   RAG_summarize.ipynb
   ```
3. This will generate `summarized.json`.

### 2. RAG Query Answering

1. Set up your Groq API key:
   ```
   export GROQ_API_KEY="your_groq_api_key_here"
   ```
   Or add the key as a secret in google colab, then run:
   ```
   import os
   from google.colab import userdata
   os.environ["GROQ_API_KEY"] = userdata.get('secretName')
   ```
2. Run the RAG script:
   ```
   actualpls_RAG.ipynb
   ```
   **Note:** You need to restart session (in google colab) after installing `anvil-uplink`
3. This will start an Anvil server that can receive queries.

## Tech Stack

### Summarization
- BART-large-CNN

### RAG Pipeline
- all-MiniLM-L6-v2 (for generating vector embeddings)
- Chroma (as a vectorstore)
- Mixtral-8x7b (as LLM, using Groq's interface)

### Deployment
- Anvil app

## Code Explanation

### RAG_summarize.ipynb

This script:
1. Loads documents from `corpus.json`.
2. Uses BART-large-CNN to generate summaries.
3. Saves summarized documents to `summarized.json`.

### actualpls_RAG.ipynb

This script:
1. Loads summarized documents from `summarized.json`.
2. Splits documents and creates a Chroma vector store.
3. Sets up a RAG pipeline using LangChain and Groq's LLM.
4. Classifies incoming queries into three types: inference, comparison, or temporal.
5. Uses appropriate prompts based on query type to generate answers.
6. Runs an Anvil server to receive queries and return answers.

## Query Classification

The current system uses a rule-based approach to classify incoming user queries. Queries are classified into three types:
- Inference: Questions starting with "which", "who", "what", etc.
- Comparison: Questions starting with "does", "do", "are", etc.
- Temporal: Questions starting with "between", "after", "has", "was", etc.

## Anvil Server

The project uses Anvil to create a server endpoint. To use this:
1. Set up an Anvil account and create a new app.
2. Replace the Anvil uplink key in the script.
3. Design your Anvil app frontend to send queries to the server.

## Limitations and Future Improvements

- The Anvil server setup might need to be replaced with a different web framework for production use.
- The query classification system could be enhanced with zero-shot classification deep learning models like BART MNLI for better accuracy.
- A confidence threshold can be set up to fetch only the relevant documents, instead of always fetching three.
- If no relevant documents are found, the query could be classified into a "null_query" and "Not enough information" could be returned as answer, so as to prevent hallucination.