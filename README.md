# Safety RAG & Document Intelligence Platform

Production-oriented AI system for industrial safety, legal QA, risk assessment, OCR, and work-standard extraction.

This repository is a public project summary based on my current working codebase. It focuses on system design, capabilities, and engineering decisions rather than publishing every internal file as-is.

## Overview

This project started as a safety-law RAG assistant and evolved into a broader document-intelligence platform for industrial safety workflows.

The current system supports:

- legal and safety QA with retrieval and reranking
- KRAS-style risk assessment assistance
- work-standard extraction from PDFs and images
- OCR/VLM-based structured field extraction
- TBM audio transcription and summarization
- asynchronous AI job processing for long-running tasks

## What Changed From the Early Version

The early public description of this project focused on older model choices and a simpler RAG prototype.

The current system is different in several important ways:

- moved to local `Qwen`-based GGUF serving through `llama-server`
- added multimodal document processing with `mmproj`
- added `GLM-OCR`-based OCR flow for table-heavy work standards
- split work-standard and risk-assessment image flows for better control
- introduced Stage1/Stage2 structured extraction pipelines
- added production-oriented server tuning for multimodal reliability
- integrated custom `llama.cpp` reasoning control work for selected workloads

## Current System Capabilities

### 1. Legal and Safety QA

- user questions are refined before retrieval
- retrieved passages are reranked before answer generation
- structured outputs are used where needed for downstream processing
- domain-specific law context is injected into answer generation

### 2. Risk Assessment Workflow

- supports process-oriented hazard and control-measure generation
- combines user-edited intermediate steps with final AI scoring flows
- supports image-assisted context for field situations
- designed for interactive review rather than one-shot generation

### 3. Work Standard Processing

- extracts work-unit candidates in Stage1
- extracts row-level fields in Stage2
- supports separate flows for:
  - standard work documents
  - risk-assessment-oriented work documents
- uses multimodal extraction, OCR fallback, and structured normalization

### 4. OCR and Multimodal Processing

- image-based analysis through local VLM serving
- OCR through `GLM-OCR` with `LlamaServerBackend`
- PDF-to-image conversion and per-page routing
- structured extraction from noisy tables and industrial document layouts

### 5. Asynchronous Inference Pipeline

- `FastAPI` for API entrypoints
- `Celery` for background jobs
- `Redis` for queueing and result polling
- long-running extraction jobs handled without blocking the UI

## Current Stack

### Models

- LLM/VLM: local `Qwen3.5-9B` GGUF via `llama-server`
- multimodal projection: local `mmproj`
- OCR: local `GLM-OCR` GGUF via `llama-server`
- STT: `WhisperX`
- embeddings: `Snowflake Arctic Embed`
- retrieval / reranking support: `FAISS`, `ColBERT`, structured law-context utilities

### Backend

- Python
- FastAPI
- Celery
- Redis
- `llama.cpp` / `llama-server`
- local GGUF deployment

### Representative Local Modules

The active codebase currently includes modules such as:

- `production_server.py`
- `general.py`
- `risk.py`
- `work_standard_image.py`
- `work_standard_2.py`
- `work_standard_ocr.py`
- `llama_server_backend.py`

## System Design Notes

### Multimodal Reliability

One of the practical challenges in this project has been multimodal stability when reusing a local `llama-server` instance across different workflows.

To reduce cross-request contamination and unstable repeated image behavior, the serving layer was tuned to be more conservative for multimodal workloads.

### Structured Extraction

The work-standard pipeline moved toward a staged design:

- Stage1 selects or normalizes candidate work units
- Stage2 extracts row fields such as:
  - work sequence
  - work method
  - equipment / tools
  - materials / consumables

This makes the system easier to debug and allows targeted retries for failed or empty responses.

### Reasoning Control

For reasoning-style models that over-generate inside `<think>...</think>`, I also built a custom `llama.cpp` extension for progressive end-token bias control.

Related repository:

- [think-control](https://github.com/tls5657/think-control)

## Public Repo Scope

This repository is intentionally positioned as a project summary / portfolio repository.

It does not try to publish every internal deployment detail. Some company-specific data, private assets, environment settings, and deployment-specific code paths are omitted or described at a higher level.

## Why This Project Matters

This project represents more than a simple chatbot or RAG demo.

It reflects hands-on work in:

- production-oriented LLM/VLM system design
- OCR and document intelligence
- retrieval + structured generation pipelines
- local inference serving with `llama.cpp`
- debugging multimodal instability in real workflows
- integrating backend systems with user-facing review loops

## Contact

- GitHub: https://github.com/tls5657
- Repository: https://github.com/tls5657/Safety_RAG_LLM
