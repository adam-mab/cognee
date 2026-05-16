# Prometheus Patches for Cognee

This branch (`prometheus-patches`) contains local patches to [Cognee](https://github.com/topoteretes/cognee) v0.5.5 that fix compatibility issues when running against **LM Studio** (or any OpenAI-compatible local inference server).

These patches are part of **[Project Prometheus](https://github.com/adam-mab/prometheus)** — an agentic second brain that uses Cognee to build a knowledge graph over a personal markdown vault.

## Patches

| # | File | Change | Why |
|---|------|--------|-----|
| 1 | `cognee/infrastructure/llm/tokenizer/TikToken/adapter.py` | `try/except KeyError` fallback to `cl100k_base` | tiktoken doesn't recognize non-OpenAI model names and throws `KeyError` |
| 2 | `cognee/infrastructure/llm/structured_output_framework/litellm_instructor/llm/openai/adapter.py` | Always apply `instructor.Mode(self.instructor_mode)` unconditionally | Original code gated instructor mode behind `if "gpt-5" in model`; LM Studio requires `json_schema_mode` for all models |
| 3 | `cognee/infrastructure/databases/vector/embeddings/LiteLLMEmbeddingEngine.py` | Only pass `dimensions` kwarg when `self.endpoint is None` | LM Studio rejects the `dimensions` parameter in embedding requests; `litellm.drop_params` doesn't catch it for embeddings |
| 4 | `cognee/infrastructure/databases/vector/embeddings/LiteLLMEmbeddingEngine.py` | `litellm.drop_params = True` at module level | Safety net for other unsupported parameters that local servers may reject |

## Upstream

- **Base**: `topoteretes/cognee` v0.5.5 (commit `5469622d`)
- **Fork**: `adam-mab/cognee`, branch `prometheus-patches`

These patches are narrow and unlikely to break OpenAI-native usage. They could be submitted upstream if there's interest.

## Usage

Install Cognee from this branch in editable mode:

```bash
pip install -e /path/to/this/repo'[docs]'
```

Then configure via environment variables or a `.env.cognee` file — see the [Prometheus SETUP SOP](https://github.com/adam-mab/prometheus/blob/main/program_management/SETUP.md) for full instructions.
