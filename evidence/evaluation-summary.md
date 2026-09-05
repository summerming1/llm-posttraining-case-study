# Evaluation Evidence Summary

This public summary describes the evaluation methodology and scope without publishing private prompts, references or model outputs.

## Evaluation evolution

The project moved beyond simple Base-vs-SFT answer screenshots into a structured evaluation system with:

- matched Base/SFT inference conditions
- blinded candidate review
- explicit failure taxonomy
- bounded-output / truncation accounting
- separate model-quality and retrieval-quality analysis

## Three-track protocol

A recent comprehensive bounded-output evaluation covers:

- **No-RAG** — 400 cases
- **Oracle-RAG** — 300 cases
- **Retrieved-RAG** — 300 cases
- Total evaluation cases: **1,000**
- Total blinded candidate reviews: **2,000**

The purpose of the three tracks is to distinguish:

1. what the SFT model learned directly,
2. whether the model can use correct evidence,
3. how much quality is lost when realistic retrieval replaces oracle evidence.

## Review policy

The recorded review keeps both favorable and unfavorable results. Factual errors, incomplete answers, missing conditions and other failure modes are retained as evidence instead of filtering for good examples.

The current candidate remains a research/engineering result rather than a production-approved model. Human-expert replication, independent-judge replication, general-capability regression and final release approval remain separate gates.

## Why this matters for client work

A fine-tuned model should be evaluated against the exact business behavior it is supposed to improve. This evaluation structure is designed to support go/no-go decisions, diagnose regressions, and separate model issues from retrieval issues.
