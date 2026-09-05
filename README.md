# 27B LLM Fine-Tuning, Evaluation & vLLM Deployment

A public, sanitized case study showing an end-to-end post-training workflow for a **27B open-weight LLM** on **4×RTX 4090** GPUs.

This repository is a portfolio case study, not the private training repository. It intentionally excludes training data, raw prompts/responses, model weights, credentials, private server paths, and customer/company information.

![Case study cover](assets/01_cover.png)

## What this case study demonstrates

- **27B LLM post-training** with 4-bit **FSDP-QLoRA** on 4×RTX 4090
- **9,571 training samples**, 202 validation samples, and 218 held-out test samples
- Base-vs-SFT model evaluation under controlled inference settings
- Blinded quality review and failure taxonomy
- Three-track evaluation design: **no-RAG / oracle-RAG / retrieved-RAG**
- Multi-GPU **vLLM** serving through an OpenAI-compatible API
- Deployment benchmarks covering concurrency **1 / 4 / 8 / 16 / 32** plus sustained-load testing
- TTFT, TPOT, ITL, throughput, GPU utilization, per-GPU memory, aggregate GPU power, and request-error tracking
- Dataset/config/adapter/workload hashing and immutable experiment-evidence records

## End-to-end workflow

![Post-training pipeline](assets/02_pipeline.png)

The key engineering goal was not simply to produce a LoRA adapter. The workflow was designed to answer:

1. Did post-training improve the target behavior?
2. What capabilities regressed?
3. How much of the final quality is caused by model behavior vs. retrieval quality?
4. Can the resulting model be served and benchmarked reproducibly on constrained multi-GPU hardware?

## Training snapshot

| Item | Observed setup |
|---|---|
| Base model | Qwen3.8-27B |
| Hardware | 4× NVIDIA RTX 4090 24GB |
| Fine-tuning | 4-bit QLoRA + FSDP FULL_SHARD |
| LoRA | rank 16, alpha 32 |
| Sequence length | 2,048 |
| Effective batch size | 32 |
| Epochs | 2 |
| Training samples | 9,571 |
| Validation samples | 202 |
| Training runtime | ~6h 42m |
| Trainable parameters | ~116.7M (~0.425%) |

Training loss is treated only as optimization evidence, not as proof of model-quality improvement.

## Evaluation engineering

![Evaluation design](assets/03_evaluation.png)

The evaluation stack evolved from a source-disjoint Base-vs-SFT benchmark into a broader three-track protocol:

- **No-RAG** — measures what the fine-tuned model learned directly
- **Oracle-RAG** — tests whether the model can use correct evidence when retrieval error is removed
- **Retrieved-RAG** — measures end-to-end behavior with frozen retrieved context

A recent bounded-output review covered **1,000 evaluation cases** and **2,000 blinded candidate reviews** across the three tracks. The evidence keeps both gains and failures visible instead of presenting only favorable examples.

> Important limitation: the evaluated model remains a research/engineering candidate, not a production-approved model. Human-expert replication, independent-judge replication, general-capability regression, and release approval remain separate gates.

## Deployment benchmark

![Deployment benchmark](assets/04_deployment.png)

The 27B Base and SFT endpoints were benchmarked with vLLM on 4×RTX 4090 using tensor parallelism.

The realistic workload covered:

- concurrency: **1, 4, 8, 16, 32**
- **300-second** sustained test at concurrency 16
- **0 observed request errors** in the recorded Base and SFT benchmark sweeps
- ~**21.77 GiB peak memory per GPU** in the recorded runs
- latency and throughput telemetry: TTFT, TPOT, ITL, requests/s and tokens/s
- GPU utilization and aggregate GPU-power telemetry

The Base endpoint reached ~**334 output tokens/s** in the recorded sustained run. Because Base and SFT produced materially different output-length distributions in the realistic workload, request-rate differences are **not** presented as isolated Dynamic-LoRA overhead. A controlled fixed-output benchmark is the correct method for that comparison.

## Evidence and reproducibility approach

The private engineering repository records immutable, sanitized evidence for dataset, training, evaluation, RAG and deployment activities. Important artifacts are identified by SHA256 rather than copied into the public case study.

```text
Dataset identity
    ↓
Training configuration + environment
    ↓
Adapter artifact identity
    ↓
Evaluation workload + blinded review
    ↓
vLLM deployment benchmark
    ↓
Release / go-no-go decision
```

See:

- [Full case study](case-study.md)
- [Training evidence summary](evidence/training-summary.md)
- [Evaluation evidence summary](evidence/evaluation-summary.md)
- [Deployment evidence summary](evidence/deployment-summary.md)

## Services this work supports

I can apply the same engineering approach to scoped client projects involving:

- LLM fine-tuning with LoRA / QLoRA / PEFT
- Fine-tuning dataset and training-pipeline diagnostics
- Base-vs-tuned model evaluation and regression analysis
- RAG quality evaluation and retrieval diagnosis
- vLLM deployment and multi-GPU inference benchmarking
- Private open-weight model serving through OpenAI-compatible APIs
- Experiment evidence, reproducibility and model go/no-go reporting

## Privacy and scope

This public repository contains **no proprietary training data, no raw model answers, no model weights, no credentials, and no private infrastructure identifiers**. Numeric results are included only where they are backed by recorded experiment evidence.
