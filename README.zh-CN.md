# OPD Speed Run

**作者：Kai Ruan** · [English](README.md) · [运行日志](NOTES.md)

在固定数据、模型和 **16 × ZW810E PPU（96 GB/卡）、3 小时**预算内，比较开源教师到学生的能力迁移方法；多轮 Agentic 赛道为 **6 小时**，使教师打分计入预算。数学、单轮代码、医学和多轮 Agentic 分别训练、分别报告。

**更新时间：2026-10-27。** 数学、代码、医学正式 run 已完成；Agentic 已完成 SFT、九轮 ReOPD 接续、一次 A+C gate、四个 exploratory 臂和两条满预算 6h 回退。这是 README-first 发布，代码与复现产物后续提供。逐轮收据与运行日志见 [NOTES.md](NOTES.md)。

## 数学

数学训练，同一个最终 checkpoint 评测数学和代码。师生：**Qwen3-1.7B ← Qwen3-4B**，均为 non-thinking。

| 算法 / 配方 | Math500 ★ | AIME 2024 ◐ | AIME 2025 ◐ | LiveCodeBench v6¹ △ |
|:--|--:|--:|--:|--:|
| 初始学生（未训练） | 73.35 | 14.38 | 10.42 | 16.43 |
| 固定教师 · Qwen3-4B | 83.45 | 26.04 | 20.83 | 25.71 |
| OPD [![GitHub](https://img.shields.io/badge/GitHub-G--OPD-181717?logo=github&logoColor=white)](https://github.com/RUCBM/G-OPD) | 74.85 | 15.42 | 14.17 | 16.71 |
| ExOPD λ=1.1 [![GitHub](https://img.shields.io/badge/GitHub-G--OPD-181717?logo=github&logoColor=white)](https://github.com/RUCBM/G-OPD) | 75.35 🥉 | 15.83 | 13.96 | 16.43 |
| ExOPD λ=1.25 [![GitHub](https://img.shields.io/badge/GitHub-G--OPD-181717?logo=github&logoColor=white)](https://github.com/RUCBM/G-OPD) | 73.35 | 14.58 | 13.96 | **17.29** 🥇 |
| Binary OPD [![GitHub](https://img.shields.io/badge/GitHub-KL--Free--OPD-181717?logo=github&logoColor=white)](https://github.com/LeapLabTHU/KL-Free-OPD) | 74.45 | 15.63 | 12.92 | 16.43 |
| IW-OPD [![GitHub](https://img.shields.io/badge/GitHub-IW--OPD-181717?logo=github&logoColor=white)](https://github.com/YannX1e/Importance-Weighted-On-Policy-Distillation) | 75.40 🥈 | **17.08** 🥇 | 14.79 🥈 | 16.29 |
| TA-OPD (tail-aware) [![GitHub](https://img.shields.io/badge/GitHub-TA--OPD-181717?logo=github&logoColor=white)](https://github.com/ml-stat-Sustech/TA-OPD) | **75.55** 🥇 | 15.63 | 14.79 🥈 | 16.57 |
| LSPD [![GitHub](https://img.shields.io/badge/GitHub-LSPD-181717?logo=github&logoColor=white)](https://github.com/UNCSciML/LSPD) | 75.25 | 13.33 | 13.33 | **17.29** 🥇 |
| ResOPD [![GitHub](https://img.shields.io/badge/GitHub-ResOPD-181717?logo=github&logoColor=white)](https://github.com/InternLM/ResOPD) | 74.00 | 16.88 🥈 | 14.38 | 16.00 |
| PowerOPD α=5 [![GitHub](https://img.shields.io/badge/GitHub-PowerOPD-181717?logo=github&logoColor=white)](https://github.com/EIT-NLP/PowerOPD) | 74.40 | 12.71 | 13.75 | 16.14 |
| AOPD [![GitHub](https://img.shields.io/badge/GitHub-AOPD-181717?logo=github&logoColor=white)](https://github.com/ObitoJN/AOPD_VeRL) | 74.45 | 13.13 | 11.67 | 17.00 🥉 |
| OPD+ (FKL) [![GitHub](https://img.shields.io/badge/GitHub-nano--opd-181717?logo=github&logoColor=white)](https://github.com/siyux1927/nano-opd) | 73.40 | 14.79 | 12.29 | 16.57 |
| IER [![GitHub](https://img.shields.io/badge/GitHub-IER--OPD-181717?logo=github&logoColor=white)](https://github.com/BruceSheng1202/IER-OPD) | 73.10 | 13.33 | 13.13 | 16.14 |
| FiRe [![GitHub](https://img.shields.io/badge/GitHub-FiRe--OPD-181717?logo=github&logoColor=white)](https://github.com/YuYingLi0/FiRe-OPD) | 74.30 | 16.88 🥈 | **15.63** 🥇 | 15.71 |

## 单轮代码

固定 12,238 条代码题训练，同一个最终 checkpoint 评测代码和数学。师生同数学赛道。

| 算法 / 配方 | LiveCodeBench v6 ★ | HumanEval+ ◐ | MBPP+ ◐ | Math500 △ |
| :-- | --: | --: | --: | --: |
| 初始学生（未训练） | 16.43 | 62.65 | 49.40 | 73.35 |
| 固定教师 · Qwen3-4B | 25.71 | 76.98 | 58.53 | 83.45 |
| OPD [![GitHub](https://img.shields.io/badge/GitHub-G--OPD-181717?logo=github&logoColor=white)](https://github.com/RUCBM/G-OPD) | 15.57 | 61.59 | 49.93 | 72.80 |
| ExOPD λ=1.1 [![GitHub](https://img.shields.io/badge/GitHub-G--OPD-181717?logo=github&logoColor=white)](https://github.com/RUCBM/G-OPD) | 17.14 🥉 | **64.48** 🥇 | 50.40 | 73.30 |
| ExOPD λ=1.25 [![GitHub](https://img.shields.io/badge/GitHub-G--OPD-181717?logo=github&logoColor=white)](https://github.com/RUCBM/G-OPD) | 16.00 | 64.18 🥈 | 49.74 | 73.55 |
| IW-OPD [![GitHub](https://img.shields.io/badge/GitHub-IW--OPD-181717?logo=github&logoColor=white)](https://github.com/YannX1e/Importance-Weighted-On-Policy-Distillation) | 16.00 | 62.04 🥉 | 50.07 | 73.00 |
| Binary OPD [![GitHub](https://img.shields.io/badge/GitHub-KL--Free--OPD-181717?logo=github&logoColor=white)](https://github.com/LeapLabTHU/KL-Free-OPD) | 16.57 | 61.28 | 50.13 | 73.15 |
| TA-OPD [![GitHub](https://img.shields.io/badge/GitHub-TA--OPD-181717?logo=github&logoColor=white)](https://github.com/ml-stat-Sustech/TA-OPD) | 16.14 | 61.89 | 50.07 | 73.60 |
| Lightning OPD [![GitHub](https://img.shields.io/badge/GitHub-Lightning--OPD-181717?logo=github&logoColor=white)](https://github.com/jet-ai-projects/Lightning-OPD) | **18.00** 🥇 | 60.21 | 47.29 | **75.15** 🥇 |
| IER [![GitHub](https://img.shields.io/badge/GitHub-IER--OPD-181717?logo=github&logoColor=white)](https://github.com/BruceSheng1202/IER-OPD) | 17.57 🥈 | 61.43 | 49.67 | 73.65 🥉 |
| FiRe [![GitHub](https://img.shields.io/badge/GitHub-FiRe--OPD-181717?logo=github&logoColor=white)](https://github.com/YuYingLi0/FiRe-OPD) | 16.43 | 61.43 | 50.00 | 73.00 |
| ResOPD [![GitHub](https://img.shields.io/badge/GitHub-ResOPD-181717?logo=github&logoColor=white)](https://github.com/InternLM/ResOPD) | 16.00 | 61.59 | 50.66 🥉 | 73.40 |
| LSPD [![GitHub](https://img.shields.io/badge/GitHub-LSPD-181717?logo=github&logoColor=white)](https://github.com/UNCSciML/LSPD) | 16.14 | 61.74 | **50.99** 🥇 | 72.85 |
| PowerOPD α=5 [![GitHub](https://img.shields.io/badge/GitHub-PowerOPD-181717?logo=github&logoColor=white)](https://github.com/EIT-NLP/PowerOPD) | 15.86 | 61.89 | **50.99** 🥇 | 73.80 🥈 |
| AOPD [![GitHub](https://img.shields.io/badge/GitHub-AOPD-181717?logo=github&logoColor=white)](https://github.com/ObitoJN/AOPD_VeRL) | 17.00 | 59.60 | 49.34 | 71.75 |
| OPD+ (FKL) [![GitHub](https://img.shields.io/badge/GitHub-nano--opd-181717?logo=github&logoColor=white)](https://github.com/siyux1927/nano-opd) | 16.29 | 59.76 | 50.40 | 72.50 |

Lightning OPD 在五阶段完成时提前收工（用掉 10,800 秒中的 7,513 秒）；其余正式 run 均为满预算（≈10,802 秒）。

## 医学

医学单域训练，沿用 [![Paper](https://img.shields.io/badge/Paper-arXiv%3A2610.13521-b31b1b)](https://arxiv.org/abs/2610.13521) 的设置。师生：**Qwen3-1.7B ← II-Medical-8B**。四个评测清单冻结为 10,607 题；训练池为 10,000 prompts（seed 42），取自污染剔除后的池。

| 算法 / 配方 | MedQA ◐ | MedXpertQA Text △ | MedMCQA ★ | SuperGPQA Medicine △ | 宏平均 | 代码保持（LCB v6¹） △ |
|:--|--:|--:|--:|--:|--:|--:|
| 初始学生（未训练） | 47.80 | 11.68 | 44.12 | 22.12 | 31.43 | 16.43 |
| 固定教师 · II-Medical-8B | 85.30 | 21.73 | 70.94 | 45.20 | 55.79 | — |
| OPD（单教师）[![GitHub](https://img.shields.io/badge/GitHub-G--OPD-181717?logo=github&logoColor=white)](https://github.com/RUCBM/G-OPD) | 47.17 | 10.70 | 46.28 | 22.82 | 31.74 | 17.43 |

每题一次生成；宏平均为四个 split 的等权平均。round-1 宏平均增益 +0.31pp，与零不可区分（McNemar p=0.106）。round-1 的 2,048 token 学生上限截断了 32.0% 的训练后响应；同一 checkpoint 在 4,096 下为 33.11（对初始学生 31.45 为 +1.66），故 round-2 起默认学生 4,096 / 教师 8,192。代码保持列不计入宏平均，未见侵蚀。

## 多轮 Agentic

正式配对（赛道负责人裁定）：教师 **Qwen3.5-9B** → 学生 **Qwen3.5-2B**。Math500 + Python 解释器：模型以 ` ```python ` 块发起工具调用、以 `<interpreter>...</interpreter>` 接收结果、以 `\boxed{}` 终结；最多 8 回合、每回合 ≤ 4,096 新 token、总计 16,384，non-thinking，本地 chroot 沙箱，math_verify 判分。正式评测为 1000 收据 @24 并发；所有比较均按 case_id 对各自父 checkpoint 逐题配对。

| 算法 / 配方 | 初值 | Pass@1（1000@24）★ | 对初值 / 对照配对 | 备注 |
|:--|:--|--:|:--|:--|
| 初始学生（未训练） | — | 45.5% | — | 同题基线 |
| 固定教师 · Qwen3.5-9B | — | 78.9 / 77.8% | 对冷 SFT 配对 +16.4 / +15.6pp | 两机 1000@24 复评 |
| SFT · .250（教师轨迹） | base | 61.5% | 对基线 +16.0pp | 16 卡 2h22m |
| SFT · .251（复现链） | base | 62.8% | 对基线 +17.3pp | 16 卡 2h18m |
| 冷 SFT 对照（r4） | base | **63.4%** | — | 同 harness 1000@24 |
| ReOPD 首轮（c，5 链）[![GitHub](https://img.shields.io/badge/GitHub-ReOPD-181717?logo=github&logoColor=white)](https://github.com/baohaoliao/ReOPD) | SFT | 61.2% | 对各自 SFT 终点 +1.2 / 0.0pp，CI 跨零 | 教师前缀回放 |
| ReOPD 后续轮（d–l）[![GitHub](https://img.shields.io/badge/GitHub-ReOPD-181717?logo=github&logoColor=white)](https://github.com/baohaoliao/ReOPD) | 链 | 58.4–62.0% | 除 k 轮 −10.8…+2.6pp 外，对上一轮均不显著 | 平台；非预算匹配 |
| A+C gate（token 级） | SFT | 60.5% | 对 63.4 −2.9pp（CI −5.3~−0.5） | FAIL，配方停止 |
| Phase 1b SSPO / centred（4 臂）[![GitHub](https://img.shields.io/badge/GitHub-SSPO-181717?logo=github&logoColor=white)](https://github.com/hkust-nlp/SSPO) | SFT | 61.4–62.2% | 对 63.4 −1.2~−2.0pp | exploratory，持平 |
| 满预算 6h 回退（r4 / .250）[![GitHub](https://img.shields.io/badge/GitHub-ReOPD-181717?logo=github&logoColor=white)](https://github.com/baohaoliao/ReOPD) | SFT | 53.3 / 60.4% | 对 63.4 −10.1 / −3.0pp | 两复现相差 −7.1pp（CI −9.9~−4.1） |
| 最佳接续：r3 · k 轮 [![GitHub](https://img.shields.io/badge/GitHub-ReOPD-181717?logo=github&logoColor=white)](https://github.com/baohaoliao/ReOPD) | 链 | 62.0% | 对其 j 轮 +2.6pp（CI +0.4~+4.9） | 血统 ≈10h，非预算匹配 |

公平预算下没有任何 OPD 臂越过自己的 SFT 初值：SFT 是目前 Agentic 最强方法，满预算前缀回放回退比它低 3–10pp 且不能跨机复现。教师 headroom 已在两机复评确认（对冷 SFT 配对 +16.4 / +15.6pp，G1 PASS）。全部 1000/1000 收据与逐轮 checkpoint 扫描见 [NOTES.md](NOTES.md)；已发表 agentic OPD 的调研、排序与下一臂选型见 [docs/agentic-opd-survey-2026-10.md](docs/agentic-opd-survey-2026-10.md)。

## 比较口径

- **分数：** 百分比，采样平均正确率（pass@1 估计）；格式错误与截断保留在分母。每个配方一次训练，seed 42；小幅差距不足以确立稳定排名。
- **奖牌：** 🥇 / 🥈 / 🥉 标记每列前三的已训练配方；并列共享同一奖牌并跳过对应的低一级奖牌（两个 🥈 即无 🥉）。初始学生与固定教师为参照行，不参与排名。粗体为列内已训练方法的最优值。
- **评测：** non-thinking、bf16。Math500 每题 4 次、AIME 16 次，T 0.7 / top-p 0.95 / 生成 ≤ 8,192 / 上下文 10,240；LCB v6¹ 冻结 175 题 ×4，HumanEval+ 164 ×4、MBPP+ 378 ×4，T 1.0 / 生成 ≤ 16,384；医学每题一次生成、T 1.0、学生 4,096 / 教师 8,192。分布标记：★ 最接近训练分布，◐ 同族偏移，△ 跨域保持。
- **预算与计账：** 数学 / 代码 / 医学为 16 PPU × 3h = 10,800 秒；Agentic 为 16 PPU × 6h = 21,600 秒（教师打分计入，评测另行计时）。时钟从 launcher 启动开始，包含初始化与检查；取截止前最后完整保存的 checkpoint。调试、资格核验与最终评测另行计账。
- **允许的算法：** SFT、在线/离线蒸馏、RL、阶段组合、教师示范、缓存与回放均计入同一预算；不引入外部数据或免费的额外已训练 checkpoint。有依据的算法专属差异予以保留，不适用的参数记为不适用。
- **配对报告：** Agentic 的 on-policy run 报告逐题配对 pass@1，对照各自父 checkpoint（case_id，n=1000，95% bootstrap CI，胜/负/平）。收据归档 OSS 并双端 md5 校验。冻结协议：[PROTOCOL.md](PROTOCOL.md) · [PROTOCOL-v2.md](PROTOCOL-v2.md)。

数学不同评测的领先方法不同；代码赛道 Lightning OPD 领先 LiveCodeBench v6 与 Math500。任何赛道都没有已训练学生超过固定教师。

## 致谢

本 benchmark 的医学单域设置——学生 Qwen3-1.7B、教师 II-Medical-8B，评测 MedQA / MedXpertQA Text / MedMCQA / SuperGPQA Medicine——沿用 Tian 等，*Beyond Domain-Routed Supervision: Cross-Teacher Guarding for Multi-Teacher On-Policy Distillation*（南京大学 & 京东，[arXiv:2610.13521](https://arxiv.org/abs/2610.13521)）中的协议。感谢作者公开记录师生配对与评测清单。

同时感谢表格中链接的开源 baseline 实现作者（G-OPD、KL-Free-OPD、IW-OPD、TA-OPD、LSPD、ResOPD、PowerOPD、AOPD、nano-opd、IER-OPD、FiRe-OPD、Lightning-OPD、SSPO、ReOPD）；他们的仓库使固定预算比较可复现。

## 引用

```bibtex
@misc{opd_speed_run_2026,
  author = {Ruan, Kai},
  title = {{OPD Speed Run}: A Fixed-Budget Benchmark for On-Policy Distillation},
  year  = {2026},
  url   = {https://github.com/x66ccff/OPD-speedrun}
}
```
