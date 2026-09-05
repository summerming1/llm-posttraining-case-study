# Training Evidence Summary

This file is a sanitized public summary of a recorded training run. It is not a copy of the private training artifacts.

## Recorded run

- Model: Qwen3.8-27B
- Training method: 4-bit QLoRA
- Distributed strategy: FSDP-QLoRA, world size 4
- Hardware: 4× NVIDIA RTX 4090 24GB
- LoRA rank / alpha: 16 / 32
- Max sequence length: 2,048
- Effective batch size: 32
- Epochs: 2
- Learning rate: 4e-5
- Training samples: 9,571
- Validation samples: 202
- Held-out test samples: 218
- Runtime: ~24,128 seconds (~6h 42m)
- Trainable parameters: ~116.7M (~0.425% of total)
- Final adapter size: ~487 MB

## Evidence controls

The private run records:

- base-model revision
- dataset identity hash
- executed training-config hash
- environment / package versions
- adapter SHA256
- trainer-state and run-metadata identities

The public case study intentionally does not include the private model path, raw data or model weights.

## Interpretation boundary

Training loss and validation loss confirm the optimization run completed; they are **not** treated as proof of downstream domain quality. Model-quality claims require separate held-out evaluation evidence.
