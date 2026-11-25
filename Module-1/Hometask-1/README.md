# Homework 1 – LLM Evaluation

## Overview

This assignment explores how to evaluate and improve LLM-generated content using an e-commerce product description generation task.

The objective is to generate product descriptions from structured product data, evaluate their quality, and experiment with different prompting and sampling strategies to improve the results.

The assignment covers two main components:

1. **LLM generation:** Using Meta-Llama-3.1-8B-Instruct to generate product descriptions.
2. **LLM evaluation:** Using Qwen3-30B-A3B-Instruct-2507 as an LLM-as-a-Judge to evaluate the generated descriptions.

The assignment also measures inference latency, token usage, and API cost.

## Technologies and Libraries

- **Python:** Main programming language.
- **Pandas:** Dataset loading and manipulation.
- **OpenAI Python SDK:** API communication with Nebius Token Factory.
- **Pydantic:** Structured output schemas and response validation.
- **OpenPyXL:** Excel export.
- **Nebius Token Factory:** Hosted LLM inference.

## Models

| Role | Model |
|---|---|
| Description generator | Meta-Llama-3.1-8B-Instruct |
| LLM evaluator | Qwen3-30B-A3B-Instruct-2507 |

Both models are accessed through the Nebius Token Factory API using the OpenAI-compatible Python SDK.

## Workflow

### 1. Define the evaluation rubric

### 2. Generate product descriptions

The input product dataset is loaded into a Pandas DataFrame.

For each product:
1. Construct a system prompt defining the copywriter's role and behavioral constraints.
2. Construct a user prompt containing the product information.
3. Call the Llama 3.1 API.
4. Extract the generated description.
5. Measure generation latency.
6. Collect input and output token counts.
7. Calculate API cost.

Generation parameters are adjusted during subsequent experiments.

### 3. Evaluate generation performance

The generated descriptions are evaluated against the rubric.

Deterministic measurements, such as word count and cost, are calculated programmatically.

### 4. Improvement Cycles

Three experiments explore ways to improve grounding.

**Prompt Engineering** included stricter constraints and few-shot examples.

**Adjusting sampling parameters** including Temp, Top-p and max output tokens.

### 5. Implement LLM-as-a-Judge

Qwen3 evaluates generated descriptions against a structured evaluation rubric.

Pydantic defines the expected response schema.

Few-shot prompting is used to clarify the grounding criteria and distinguish unsupported product-specific claims from subjective marketing language. Chain-of-thought prompting is used as well.

### 6. Compare human and LLM evaluation


## Key Machine Learning Concepts

| Concept | What it covers |
|---|---|
| Tokenization | Representing text as tokens |
| LLM inference | Next-token prediction and text generation |
| Hallucination | Generation of unsupported or incorrect information |
| Prompt engineering | Controlling model behavior through instructions and examples |
| Few-shot prompting | Guiding model behavior through examples |
| Chain-of-thought prompting | Guiding model behavior by enforcing step-by-step reasoning before producing a final answer |
| Temperature | Controlling randomness in token sampling |
| Top-p sampling | Nucleus sampling and candidate token selection |
| LLM-as-a-Judge | Using an LLM to evaluate another LLM's output |
| Structured outputs | Enforcing machine-readable response formats |
| Human-in-the-loop | Combining automated and human evaluation |

## Main Takeaways

- LLM output quality requires explicit evaluation criteria.
- Fluency does not guarantee factual grounding.
- Prompt engineering and sampling parameters can influence generation quality.
- LLMs can automate evaluation, but their judgments must also be evaluated.
- Deterministic tasks should be handled programmatically whenever possible.
- A hybrid human/LLM evaluation approach can balance scalability and accuracy.
- Inference cost and latency are important considerations when designing production LLM applications.
