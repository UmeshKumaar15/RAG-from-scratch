# RAG From Scratch

This project implements a simple Retrieval-Augmented Generation (RAG) pipeline using LangChain, FAISS, HuggingFace embeddings, and a lightweight LLM (TinyLlama/Falcon).

The system allows users to ask questions based on a PDF document, and the model answers using retrieved context from that document.

---

## Project Overview

This project demonstrates how a basic RAG system works end-to-end.

The pipeline consists of four main stages:

1. Data Ingestion  
2. Text Chunking  
3. Embeddings + Vector Store  
4. LLM + Retrieval QA  

The goal is to:
- Convert a PDF into searchable embeddings
- Retrieve relevant chunks based on a query
- Generate an answer grounded in the retrieved context

---

## Step 1: Data Ingestion

- A PDF file is uploaded to Google Colab.
- `PyPDFLoader` from LangChain is used to extract text.
- Each page of the PDF is converted into a `Document` object.

At this stage:
- Raw PDF text is loaded.
- Metadata (page number, source) is preserved.

---

## Step 2: Text Chunking

- The text is split using `RecursiveCharacterTextSplitter`.
- Chunk size: 800 characters
- Overlap: 100 characters

Why chunking is required:
- LLMs have token limits.
- Smaller chunks improve retrieval accuracy.
- Overlap preserves context continuity between chunks.

Output:
- A list of semantically meaningful text chunks ready for embedding.

---

## Step 3: Embeddings and Vector Store

### Embeddings

- Model used: `all-MiniLM-L6-v2`
- Converts each text chunk into a dense numerical vector.

Process:
```
Text Chunk → Embedding Vector
```

These vectors capture semantic meaning.

---

### FAISS Vector Store

- FAISS is used to store and index embeddings.
- Enables efficient similarity search.
- Retrieval is based on cosine similarity.

At query time:
- The question is embedded.
- Top-k similar chunks are retrieved.
- These chunks are passed to the LLM as context.

---

## Step 4: LLM + Retrieval Pipeline

A lightweight instruction model (TinyLlama or Falcon) is used for answer generation.

The prompt forces the model to:
- Use only the provided context
- Say "I don't know" if the answer is not found

RAG Flow:

```
User Question
        ↓
Retriever (FAISS similarity search)
        ↓
Top-K Relevant Chunks
        ↓
Prompt Template (context + question)
        ↓
LLM generates the final answer
```

The retriever supplies context.
The LLM generates the final response using that context.

---

## Prompt Design

The prompt used:

```
Use ONLY the context below to answer the question.
If the answer is not in the context, say "I don't know".

Context:
{context}

Question:
{question}
```

This reduces hallucination and enforces context grounding.

---

## Example

Query:
```
Explain the Hamming code in simple terms
```

The system:
- Retrieves relevant chunks from the PDF
- Passes them into the prompt
- Generates a simplified explanation

If the question is unrelated to the document:
```
Who is the most decorated swimmer in the world?
```

The expected behavior (after threshold filtering):
```
I don't know
```

---

## Learnings & Future upgrades

- Small LLMs (TinyLlama). (Should use stronger instruction models like Mistral / GPT-4o)
- No automated evaluation metrics implemented. (Add evaluation metrics like precision@k / recall@k)
- No re-ranking or answer verification layer.

It serves as a hands-on implementation for learning RAG systems from scratch.

---
