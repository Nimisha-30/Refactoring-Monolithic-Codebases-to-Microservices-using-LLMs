# Refactoring Monolithic Codebases to Microservices using LLMs

A Retrieval-Augmented Generation (RAG) system that ingests large monolithic codebases and generates microservice decomposition strategies — rewriting functions as REST, gRPC, or GraphQL APIs with accompanying client code.

## What it does

The system indexes an existing codebase (demonstrated on NumPy's `linalg` module and several standalone C++/Python scripts) using LlamaIndex, then uses a fine-tuned instruction-following LLM to answer architecture-level queries such as:

- "Rewrite this function as a REST API with a client function that calls it"
- "Convert this into a gRPC service"
- "Refactor into GraphQL-based APIs"
- "Apply modular programming principles: encapsulation, abstraction, loose coupling"

The output is directly usable refactored code, grounded in the retrieved source rather than generated from scratch — reducing hallucination risk compared to prompting an LLM with no codebase context.

## How it works

1. **Document ingestion** — Source files (Python, C++) are loaded via `SimpleDirectoryReader` and chunked (2048 tokens/chunk).
2. **Embedding** — Chunks are embedded using `sentence-transformers/all-mpnet-base-v2` via HuggingFace, wrapped for LlamaIndex compatibility.
3. **Indexing** — A `VectorStoreIndex` is built over the embedded chunks.
4. **Generation** — `CodeLlama-7b-Instruct-hf` is used as the query-answering LLM, with a system prompt constraining it to accurate, context-grounded code rewrites.
5. **Query engine** — Natural-language architecture requests are issued against the index, returning refactored code plus explanations.

## Research grounding

Decomposition logic is informed by published system design literature on monolith-to-microservice migration (included in this repo as reference PDFs), rather than ad hoc prompting.

## Tech stack

`Python` · `LlamaIndex` · `LangChain` · `HuggingFace Transformers` · `CodeLlama-7b-Instruct` · `sentence-transformers` · `Google Colab`

## Files

- `RAG.ipynb` / `RAG (1).ipynb` — Main pipeline: ingestion, indexing, query engine, refactoring queries
- `sample-code/` — Source snippets used as ingestion targets
- Reference PDFs — Research papers on monolith decomposition that grounded the approach

## Notes

This was built as an exploratory research project on using RAG for architecture-level code transformation, rather than a production migration tool. Results are query-dependent and should be reviewed before use in a real refactor.
