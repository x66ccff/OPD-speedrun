# OPD Speed Run

**Author: Kai Ruan** · [中文](README.zh-CN.md)

Compare open-source teacher-to-student transfer methods with fixed data, models, and a budget of **16 × ZW810E PPU cards (96 GB each) for three hours**. Math, Single-turn Code, and Multi-turn Agentic are separate training tracks with separate results.

**Updated October 19, 2026.** Evaluation is complete for 13 Math recipes and 4 Code recipes. This is a README-first release; code and reproduction artifacts will follow.

## Math

Train on math; evaluate the same final checkpoint on math and code. Models: **Qwen3-1.7B ← Qwen3-4B**, both non-thinking.

| Method / recipe | Math500 | AIME 2024 | AIME 2025 | LiveCodeBench v6¹ |
|:--|--:|--:|--:|--:|
| Initial student (no training) | 73.35 | 14.38 | 10.42 | 16.43 |
| OPD | 74.85 | 15.42 | 14.17 | 16.71 |
| ExOPD λ=1.1 | 75.35 | 15.83 | 13.96 | 16.43 |
| ExOPD λ=1.25 | 73.35 | 14.58 | 13.96 | **17.29** |
| Binary OPD | 74.45 | 15.63 | 12.92 | 16.43 |
| IW-OPD | 75.40 | **17.08** | 14.79 | 16.29 |
| TA-OPD (tail-aware) | **75.55** | 15.63 | 14.79 | 16.57 |
| LSPD | 75.25 | 13.33 | 13.33 | **17.29** |
| ResOPD | 74.00 | 16.88 | 14.38 | 16.00 |
| PowerOPD α=5 | 74.40 | 12.71 | 13.75 | 16.14 |
| AOPD | 74.45 | 13.13 | 11.67 | 17.00 |
| OPD+ (FKL) | 73.40 | 14.79 | 12.29 | 16.57 |
| IER | 73.10 | 13.33 | 13.13 | 16.14 |
| FiRe | 74.30 | 16.88 | **15.63** | 15.71 |

## Single-turn Code

Train on 12,238 fixed code prompts; evaluate the same final checkpoint on code and math. Models match the Math track.

| Method / recipe | LiveCodeBench v6 | HumanEval+ | MBPP+ | Math500 |
| :-- | --: | --: | --: | --: |
| Initial student (no training) | 16.43 | 62.65 | 49.40 | 73.35 |
| OPD | 15.57 | 61.59 | 49.93 | 72.80 |
| ExOPD λ=1.1 | **17.14** | **64.48** | **50.40** | 73.30 |
| ExOPD λ=1.25 | 16.00 | 64.18 | 49.74 | **73.55** |
| IW-OPD | 16.00 | 62.04 | 50.07 | 73.00 |

## Multi-turn Agentic

Tool-interaction tasks. Teacher selection, tool protocol, and formal evaluation remain under qualification. **— means no formal score yet.**

| Model / method | Task success | Status |
|:--|--:|:--|
| Initial student · Qwen3-1.7B | — | Development evaluation; formal scores pending |
| Candidate teacher · Qwen3-32B | — | Teacher–student compatibility and task capability under qualification |
| Vanilla OPD | — | Two development updates on 16 cards passed; formal scores pending |

## Comparison protocol

- **Scores:** percentages, mean sample correctness (a pass@1 estimate). Each recipe currently has one training run, seed 42. Bold marks the highest observed value in each column.
- **Evaluation:** Math500 uses 4 samples per task; each AIME set uses 16. LCB v6¹ uses a frozen 175-task subset with 4 samples per task. HumanEval+ covers 164 tasks and MBPP+ covers 378, each with 4 samples and the pinned official EvalPlus evaluator. Format errors and truncations remain in the denominator.
- **Shared conditions:** lock data and field permissions, initial teacher/student weights, hardware, time, n, learning rate, sampling, and formal evaluation within each track. Current Math/Code training uses n=1, learning rate 1e-6, temperature=1, top-p=1, and top-k disabled.
- **Allowed methods:** SFT, online/offline distillation, RL, staged combinations, teacher demonstrations, caching, and replay all count toward the same budget. Preserve justified algorithm-specific differences; no external data or free additional trained checkpoints. Mark inapplicable parameters as such.
- **Accounting and selection:** the clock starts at launcher invocation, including initialization, checks, and all training stages. Evaluate the last complete checkpoint saved before the deadline. Debugging, qualification, and final evaluation are accounted for separately. Agentic runs use a shared sandbox pool; environment queueing, execution, and cleanup count toward the budget, with contention reported.

Different methods lead on different Math evaluations. ExOPD λ=1.1 leads all three Code-track code evaluations. Small differences from one training seed do not establish stable rankings. Lightning OPD and ULD have no formal scores yet.

## Citation

```bibtex
@misc{opd_speed_run_2026,
  author = {Ruan, Kai},
  title = {{OPD Speed Run}: A Fixed-Budget Benchmark for On-Policy Distillation},
  year  = {2026},
  url   = {https://github.com/x66ccff/OPD-speedrun}
}
```
