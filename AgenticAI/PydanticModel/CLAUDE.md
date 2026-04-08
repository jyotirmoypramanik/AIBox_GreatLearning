# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **Great Learning Agentic AI course project** demonstrating prompt chaining with Pydantic validation. The primary artifact is a Jupyter notebook (`notebooks/00_pydantic_model.ipynb`) that builds an automated insurance claim triage pipeline.

The notebook implements a 3-stage LLM prompt chain for processing auto insurance FNOL (First Notice of Loss) reports:
1. **Stage I — Information Extraction**: Parse free-form claim text into structured `ClaimInformation` via Pydantic
2. **Stage II — Severity Assessment**: Classify damage as Minor/Moderate/Major with cost estimates, validated by `SeverityAssessment`
3. **Stage III — Queue Routing**: Route claims to `glass`, `fast_track`, `material_damage`, or `total_loss` queues via `ClaimRouting`

Each stage has a gate-check function (`gate1_validate_claim_info`, `gate2_cost_range_ok`, `gate3_validate_routing`) that validates LLM output against Pydantic models before passing to the next stage.

## Key Dependencies

- `openai` — LLM calls (uses Vocareum endpoint for the course, or OpenAI directly)
- `pydantic` — structured output validation with `BaseModel` and `Field`
- `pandas` — final output review

## Configuration

- `config/.env` — active environment config (LLM keys, analysis mode, RAG settings)
- `config/.env.example` — template with Oracle DB monitoring fields (from a related project)
- LLM endpoint defaults to `https://openai.vocareum.com/v1`; requires `OPENAI_API_KEY`

## Running the Notebook

Open `notebooks/00_pydantic_model.ipynb` in Jupyter and run cells sequentially. An API key must be set in cell 2 before execution.

## Project Layout

- `notebooks/` — Jupyter notebooks (main working area)
- `src/`, `tests/`, `data/`, `docs/` — placeholder directories for future expansion
