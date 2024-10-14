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


## How to Run

### 1. Document Summarization

1. Ensure you have `corpus.json` in your project directory.
2. Run the summarization script in google colab/locally (needs GPU):
   ```
   RAG_summarize.ipynb
   ```
3. This will generate `summarized.json`.

### 2. RAG Query Answering

1. Run the RAG script:
   ```
   actualpls_RAG.ipynb
   ```
   **Note:** You need to restart session (in google colab) after installing `anvil-uplink`
2. This will start an Anvil server that can receive queries.

## Tech Stack

- LangChain
- sentence transformer
- pyTorch

### Summarization
- BART-large-CNN

### RAG Pipeline
- all-MiniLM-L6-v2 (for generating vector embeddings)
- Chroma (as a vectorstore)
- Flan T5 Large

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
3. Sets up a RAG pipeline using LangChain.
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
- A hybrid query classification system could be implemented with zero-shot classification deep learning models like BART MNLI reinforcing the initial prediction for better accuracy.