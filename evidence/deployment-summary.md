# Deployment Evidence Summary

This public summary describes the recorded vLLM serving benchmark without exposing endpoint URLs, credentials, prompts, completions or private server paths.

## Serving setup

- Model family: Qwen3.8-27B
- Hardware: 4× NVIDIA RTX 4090 24GB
- vLLM: 0.27.1 in the recorded benchmark
- Dtype: bfloat16
- Tensor parallel: 4
- Max model length: 8,192
- Base and SFT variants served separately for comparison

## Realistic workload benchmark

Recorded concurrency sweep:

- 1
- 4
- 8
- 16
- 32

Additional sustained run:

- concurrency: 16
- target duration: 300 seconds

Recorded telemetry includes:

- TTFT p50/p95/p99
- TPOT p50/p95/p99
- inter-token latency
- requests/s
- input/output/aggregate token throughput
- GPU utilization
- per-GPU peak memory
- aggregate GPU power
- request error rate

Both the recorded Base and SFT benchmark sweeps observed **0% request errors**. Peak memory was approximately **21.77 GiB per GPU** in the recorded runs.

The Base sustained run reached approximately **334 output tokens/s** on this realistic workload.

## Interpretation boundary

The realistic workload allows models to stop naturally. Base and SFT produced materially different output-length distributions, so differences in requests/s or output tokens/s cannot be interpreted as isolated Dynamic-LoRA overhead.

The private benchmark tooling was subsequently extended with a `synthetic-fixed` mode that forces matched completion length for controlled serving-overhead comparison.

## Why this matters for client work

For private LLM deployments, “the endpoint starts” is not enough. Capacity decisions require latency percentiles, throughput, memory headroom, GPU utilization, error rates and a clearly defined workload. This benchmark structure is intended to support those decisions.
