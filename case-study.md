# Case Study: 27B Domain LLM Post-Training on 4×RTX 4090

## Problem

The project was designed around a practical question: **how can a domain LLM be fine-tuned, evaluated and served on constrained hardware without confusing training progress with real model improvement?**

The target was a 27B open-weight model adapted to a specialized metallurgy QA domain. The engineering work covered training, evaluation, RAG diagnosis, serving and experiment evidence.

## Constraints

- 4×RTX 4090 24GB GPUs
- private domain data
- open-weight model serving
- need to keep Base and SFT variants directly comparable
- need to avoid leaking raw prompts, answers, model outputs and server details into public artifacts
- need to preserve enough evidence to audit what model/data/config produced each result

## Training approach

The final recorded training run used:

- Qwen3.8-27B
- 4-bit NF4 QLoRA
- FSDP FULL_SHARD across 4 GPUs
- LoRA rank 16 / alpha 32
- max sequence length 2,048
- effective batch size 32
- 2 epochs
- 9,571 training samples / 202 validation samples

The run completed in roughly 6 hours 42 minutes. Dataset identity, configuration identity and adapter identity were recorded separately so later evaluation could be tied back to a concrete training artifact.

## Evaluation approach

The project deliberately treats **training loss as optimization evidence only**. Quality decisions are made with held-out evaluation and blinded review.

The evaluation stack includes:

1. Base-vs-SFT comparisons under matched generation settings
2. source-disjoint evaluation controls where supported by the frozen benchmark package
3. blinded candidate review
4. failure taxonomy for factual errors, missing conditions, incompleteness and related failure modes
5. bounded-output tracking so truncated answers are not silently dropped
6. three evaluation tracks:
   - no-RAG
   - oracle-RAG
   - retrieved-RAG

The three-track design helps separate model-learning problems from evidence-use problems and retrieval problems.

A recent bounded-output review covers 1,000 evaluation cases and 2,000 blinded candidate reviews across these tracks. The result is intentionally treated as diagnostic evidence, not a production-release certificate.

## Deployment approach

The Base and SFT variants were served through vLLM on 4×RTX 4090 using tensor parallelism and an OpenAI-compatible API.

The realistic deployment benchmark records:

- concurrency 1 / 4 / 8 / 16 / 32
- 300-second sustained load at concurrency 16
- TTFT, TPOT and inter-token latency
- request and token throughput
- GPU utilization
- per-GPU peak memory
- aggregate GPU power
- request error rate

Recorded Base and SFT benchmark sweeps both observed 0% request errors. Per-GPU peak memory was approximately 21.77 GiB in the recorded runs.

One important lesson from the benchmark was that realistic workload throughput cannot be used blindly to estimate Dynamic-LoRA overhead: Base and SFT outputs had materially different length distributions. The benchmark framework was therefore extended with a controlled fixed-output mode for isolated serving-overhead comparison.

## Evidence engineering

A major part of the project is the evidence sidecar rather than only the model code. The system records sanitized, immutable run evidence for:

- datasets
- training
- evaluation
- RAG
- deployment
- release decisions

Where possible, identities are bound with SHA256 hashes. Unknown fields remain explicit instead of being reconstructed from assumptions.

This allows later questions such as “which data and adapter produced this benchmark?” or “was this evaluation run generated under the same conditions?” to be answered from recorded artifacts.

## What went well

- 27B multi-GPU QLoRA training completed on consumer GPUs
- Base and SFT endpoints were served and benchmarked on the same 4-GPU system
- evaluation was expanded beyond answer screenshots into blinded and multi-track testing
- negative findings and regressions are preserved rather than filtered out
- deployment telemetry includes latency, throughput and hardware utilization

## Remaining gaps / next engineering steps

- human-expert replication of model-quality review
- independent AI-judge replication
- general-capability regression testing
- approved production release gate
- controlled fixed-output Base-vs-LoRA serving comparison
- production gateway concerns such as authentication, TLS, rate limits, alerting and recovery

## Relevance to client work

This project demonstrates the workflow needed for scoped consulting or implementation work such as:

- diagnosing why a fine-tuning run did not improve a model
- implementing LoRA/QLoRA training on constrained GPU hardware
- building Base-vs-tuned evaluation and regression pipelines
- auditing RAG quality separately from model quality
- deploying private open-weight models with vLLM
- benchmarking latency, throughput and GPU capacity
- creating auditable experiment evidence for model go/no-go decisions
