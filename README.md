# OPD Speed Run

[中文](README.zh-CN.md)

**Fixed models. Fixed data. Fixed hardware. Fixed training time.**

OPD Speed Run compares on-policy distillation algorithms under a shared compute budget: how much can a small student improve by learning from a fixed, larger teacher in three hours?

The current track focuses on single-turn math and code with **Qwen3-1.7B ← Qwen3-4B**. We reproduce existing methods under a common protocol and measure their final-checkpoint performance within the same resource budget.

> **Release status:** Results and protocol are available here. Training code, evaluation tools, configuration manifests, and reproducibility artifacts will follow.

## Results

Snapshot: **October 17, 2026**. Seven training recipes have completed the fixed-budget run and all four evaluation sets. Scores are percentages; bold indicates the highest observed score in each column.

| Method / recipe | Math500 | AIME 2024 | AIME 2025 | LiveCodeBench v6¹ |
|:--|--:|--:|--:|--:|
| Initial student (no training) | 73.35 | 14.38 | 10.42 | 16.43 |
| OPD | 74.85 | 15.42 | 14.17 | 16.71 |
| ExOPD λ=1.1 | 75.35 | 15.83 | 13.96 | 16.43 |
| ExOPD λ=1.25 | 73.35 | 14.58 | 13.96 | **17.29** |
| Binary OPD | 74.45 | 15.63 | 12.92 | 16.43 |
| IW-OPD | 75.40 | **17.08** | **14.79** | 16.29 |
| TA-OPD (tail-aware) | **75.55** | 15.63 | **14.79** | 16.57 |
| LSPD | 75.25 | 13.33 | 13.33 | **17.29** |

¹ LiveCodeBench v6 scores use a frozen **175-task subset**.

Scores estimate **pass@1** by averaging correctness across sampled responses. Each recipe uses **one training run with seed 42**; rankings across training seeds remain to be measured.

**Results at a glance:** IW-OPD leads on AIME 2024. Tail-aware TA-OPD leads on Math500 and ties IW-OPD on AIME 2025. ExOPD λ=1.25 and LSPD tie on code, with a tradeoff in math performance. The margins are small: TA-OPD exceeds IW-OPD by 3/2,000 correct Math500 responses, and the code leaders exceed ordinary OPD by 4/700.

ExOPD λ=1.25 follows a public recommendation; λ=1.1 provides an additional comparison. We report both recipes separately.

## Fixed-budget protocol

| Component | Current track |
|:--|:--|
| Student | Qwen3-1.7B, initialized from the same original HF weights for each run |
| Teacher | Frozen Qwen3-4B; external larger teacher |
| Thinking mode | Non-thinking for both student and teacher |
| Training data | The same 56,107-prompt DeepMath-derived prompt-only asset |
| Hardware | One node, 16 × ZW810E PPU, 96 GB per device |
| Time limit | 10,800 seconds from launcher start, including initialization and checks |
| Resource allocation | 8 actor devices, 6 rollout devices, 2 teacher devices |
| Training batch | 256 prompts; one sampled response per prompt |
| Training sampling | Temperature 1.0, top-p 1.0, top-k disabled |
| Training lengths | Maximum response: 8,192 tokens; context: 32,768 tokens |
| Optimizer | Adam, constant learning rate 1e-6, no warmup; seed 42 |
| Loss reduction | Mean over valid tokens within each response, then mean over responses |
| Checkpoint selection | Last complete checkpoint saved before the deadline |

Each formal run receives **48 allocated PPU-device-hours**. Qualification, debugging, failed attempts, and final evaluation are accounted for separately. Utilization varies across rollout, scoring, and update phases.

We fix shared experimental conditions and preserve each method's objective and algorithm-specific parameters. Reproductions record the adaptations from author defaults to this common protocol.

## Evaluation

All checkpoints use the same frozen prompts, decoding settings, scoring logic, and task IDs. Checkpoint selection follows the time-budget rule above.

| Evaluation set | Tasks | Samples per task | Temperature | Top-p | Max. response tokens |
|:--|--:|--:|--:|--:|--:|
| Math500 | 500 | 4 | 0.7 | 0.95 | 8,192 |
| AIME 2024 | 30 | 16 | 0.7 | 0.95 | 8,192 |
| AIME 2025 | 30 | 16 | 0.7 | 0.95 | 8,192 |
| LiveCodeBench v6, fixed slice | 175 | 4 | 1.0 | 1.0 | 16,384 |

That is **3,660 evaluated responses per checkpoint**. Math uses a 10,240-token context limit and code uses 24,576; evaluation seed is 20261014. Code correctness is determined by executing tests. Evaluation retains format errors and truncated responses in the original sample set.

## Reproduction checks

Before formal admission, candidates undergo checks of actual backward execution, full-batch updates, checkpoint/resume consistency, data-cursor continuity, export/reload, and matched-trajectory rescoring. Training-entry checks enforce the registered configuration before resource allocation. Qualification records bind each result to its tested runtime and configuration.

The current track covers Qwen3-1.7B ← Qwen3-4B on ZW810E with a three-hour budget. Data auditing covers exact deduplication; semantic and pretraining contamination remain unaudited.

## Next steps

- Complete ResOPD and PowerOPD qualification and evaluation.
- Extend the benchmark to more open-source OPD methods.
- Release the training code, frozen evaluation tools, and exact configuration manifests.

## Citation

If you use this benchmark or its results, please cite:

```bibtex
@misc{opd_speed_run_2026,
  title = {{OPD Speed Run}: A Fixed-Budget Benchmark for On-Policy Distillation},
  author = {Ruan, Kai},
  year  = {2026}
}
```
