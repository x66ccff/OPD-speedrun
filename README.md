# OPD Speed Run

**Author: Kai Ruan** · [中文](README.zh-CN.md) · [Run log](NOTES.md)

Compare open-source teacher-to-student transfer methods with fixed data, models, and a budget of **16 × ZW810E PPU cards (96 GB each) for three hours**; the Multi-turn Agentic track uses **six hours** so that teacher scoring fits inside the budget. Math, Single-turn Code, Medical and Multi-turn Agentic are separate training tracks with separate results.

**Updated October 27, 2026.** Math, Code and Medical formal runs are complete. Agentic has SFT, nine ReOPD continuation stages, an A+C gate, four exploratory arms and two full-budget 6 h fallbacks. This is a README-first release; code and reproduction artifacts will follow. Per-run receipts and the full run log: [NOTES.md](NOTES.md).

## Math

Train on math; evaluate the same final checkpoint on math and code. Models: **Qwen3-1.7B ← Qwen3-4B**, both non-thinking.

| Method / recipe | Math500 ★ | AIME 2024 ◐ | AIME 2025 ◐ | LiveCodeBench v6¹ △ |
|:--|--:|--:|--:|--:|
| Initial student (no training) | 73.35 | 14.38 | 10.42 | 16.43 |
| Fixed teacher · Qwen3-4B | 83.45 | 26.04 | 20.83 | 25.71 |
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

| Method / recipe | LiveCodeBench v6 ★ | HumanEval+ ◐ | MBPP+ ◐ | Math500 △ |
| :-- | --: | --: | --: | --: |
| Initial student (no training) | 16.43 | 62.65 | 49.40 | 73.35 |
| Fixed teacher · Qwen3-4B | 25.71 | 76.98 | 58.53 | 83.45 |
| OPD | 15.57 | 61.59 | 49.93 | 72.80 |
| ExOPD λ=1.1 | 17.14 | **64.48** | 50.40 | 73.30 |
| ExOPD λ=1.25 | 16.00 | 64.18 | 49.74 | 73.55 |
| IW-OPD | 16.00 | 62.04 | 50.07 | 73.00 |
| Binary OPD | 16.57 | 61.28 | 50.13 | 73.15 |
| TA-OPD | 16.14 | 61.89 | 50.07 | 73.60 |
| Lightning OPD | **18.00** | 60.21 | 47.29 | **75.15** |
| IER | 17.57 | 61.43 | 49.67 | 73.65 |
| FiRe | 16.43 | 61.43 | 50.00 | 73.00 |
| ResOPD | 16.00 | 61.59 | 50.66 | 73.40 |
| LSPD | 16.14 | 61.74 | **50.99** | 72.85 |
| PowerOPD α=5 | 15.86 | 61.89 | **50.99** | 73.80 |
| AOPD | 17.00 | 59.60 | 49.34 | 71.75 |
| OPD+ (FKL) | 16.29 | 59.76 | 50.40 | 72.50 |

Lightning OPD finished its five training stages early (7,513 s of the 10,800 s budget); every other formal run used the full envelope (≈10,802 s).

## Medical

Single-domain medical training. Models: **Qwen3-1.7B ← II-Medical-8B**. The four evaluation lists are frozen at 10,607 questions; the training pool is 10,000 prompts (seed 42) drawn from contamination-excluded pools.

| Method / recipe | MedQA ◐ | MedXpertQA Text △ | MedMCQA ★ | SuperGPQA Medicine △ | Macro average | Code retention (LCB v6¹) △ |
|:--|--:|--:|--:|--:|--:|--:|
| Initial student (no training) | 47.80 | 11.68 | 44.12 | 22.12 | 31.43 | 16.43 |
| Fixed teacher · II-Medical-8B | 85.30 | 21.73 | 70.94 | 45.20 | 55.79 | — |
| OPD (single teacher) | 47.17 | 10.70 | 46.28 | 22.82 | 31.74 | 17.43 |

One completion per question; macro is the equal-weight mean of the four splits. The round-1 macro gain of +0.31pp is not distinguishable from zero (McNemar p=0.106). The round-1 2,048-token student cap truncated 32.0% of trained responses; the same checkpoint scores 33.11 at 4,096 (+1.66 over the initial student's 31.45), so round 2 defaults to student 4,096 / teacher 8,192. Code retention is measured outside the macro and shows no erosion.

## Multi-turn Agentic

Formal pair (track-owner decision): teacher **Qwen3.5-9B** → student **Qwen3.5-2B**. Math500 with a Python interpreter: the model opens a tool call with a ` ```python ` block, receives `<interpreter>...</interpreter>` results and terminates with `\boxed{}`; at most 8 turns, ≤ 4,096 new tokens per turn, 16,384 total, non-thinking, local chroot sandbox, math_verify. Formal evaluation is 1000 receipts at concurrency 24; every comparison is per-question paired by case_id against the run's own parent checkpoint.

| Method / recipe | Init | Pass@1 (1000@24) ★ | Paired vs init / control | Note |
|:--|:--|--:|:--|:--|
| Initial student (no training) | — | 45.5% | — | same-slice baseline |
| Fixed teacher · Qwen3.5-9B | — | 79.6% | — | headroom reference |
| SFT · .250 (teacher trajectories) | base | 61.5% | +16.0pp vs baseline | 2h22m on 16 cards |
| SFT · .251 (replication) | base | 62.8% | +17.3pp vs baseline | 2h18m on 16 cards |
| Cold SFT control (r4) | base | **63.4%** | — | same-harness 1000@24 |
| ReOPD first round (c, 5 runs) | SFT | 61.2% | +1.2 / 0.0pp vs SFT endpoints, CI cross zero | teacher-prefix replay |
| ReOPD later rounds (d–l) | chain | 58.4–62.0% | no stage significant vs its own prev round except k: −10.8…+2.6pp | plateau; not budget-matched |
| A+C gate (token-level) | SFT | 60.5% | −2.9pp vs 63.4 (CI −5.3 to −0.5) | FAIL, recipe stopped |
| Phase 1b SSPO / centred (4 arms) | SFT | 61.4–62.2% | −1.2 to −2.0pp vs 63.4 | exploratory, flat |
| Full-budget 6 h fallback (r4 / .250) | SFT | 53.3 / 60.4% | −10.1 / −3.0pp vs 63.4 | two hosts differ −7.1pp (CI −9.9 to −4.1) |
| Best continuation: r3 · k round | chain | 62.0% | +2.6pp vs its j round (CI +0.4 to +4.9) | lineage ≈10 h, not budget-matched |

No OPD arm beats its own SFT initialisation under an equal budget: SFT is the strongest Agentic method so far, and the full-budget prefix-replay fallback is 3–10pp below it on both hosts and does not reproduce across hosts. All 1000/1000 receipts and the round-by-round checkpoint sweep are in [NOTES.md](NOTES.md).

## Comparison protocol

- **Scores:** percentages, mean sample correctness (a pass@1 estimate); format errors and truncations stay in the denominator. Bold marks the highest trained value in each column. Each recipe has one training run, seed 42; small gaps do not establish stable rankings.
- **Evaluation:** non-thinking, bf16. Math500 4 samples/task and AIME 16, T 0.7 / top-p 0.95 / gen ≤ 8,192 / context 10,240; LCB v6¹ 175-task frozen subset ×4, HumanEval+ 164 ×4 and MBPP+ 378 ×4, T 1.0 / gen ≤ 16,384; Medical one completion per question, T 1.0, student 4,096 / teacher 8,192 caps. Distribution tags: ★ closest to the training distribution, ◐ same family shifted, △ cross-domain retention.
- **Budget and accounting:** 16 PPU × 3 h = 10,800 s for Math / Code / Medical; 16 PPU × 6 h = 21,600 s for Agentic, with teacher scoring included and evaluation timed separately. The clock starts at launcher invocation, including initialization and checks; evaluate the last complete checkpoint saved before the deadline. Debugging, qualification and final evaluation are accounted for separately.
- **Allowed methods:** SFT, online/offline distillation, RL, staged combinations, teacher demonstrations, caching and replay all count toward the same budget; no external data or free extra trained checkpoints. Justified algorithm-specific differences are preserved and inapplicable parameters marked as such.
- **Paired reporting:** Agentic on-policy runs report per-question paired pass@1 against their own parent checkpoint (case_id, n=1000, 95% bootstrap CI, W/L/T). Receipts are archived on OSS with md5 verified on both ends. Frozen protocols: [PROTOCOL.md](PROTOCOL.md) · [PROTOCOL-v2.md](PROTOCOL-v2.md).

Different methods lead on different Math evaluations; Lightning OPD leads LiveCodeBench v6 and Math500 in the Code track. No trained student exceeds its fixed teacher on any track.

## Citation

```bibtex
@misc{opd_speed_run_2026,
  author = {Ruan, Kai},
  title = {{OPD Speed Run}: A Fixed-Budget Benchmark for On-Policy Distillation},
  year  = {2026},
  url   = {https://github.com/x66ccff/OPD-speedrun}
}
```
