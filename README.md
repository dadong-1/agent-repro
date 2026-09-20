# Agent 复现周记

## 本周复现：Reflexion (NeurIPS'23)

- 论文：Reflexion: Language Agents with Verbal Reinforcement Learning
- 官方代码：noahshinn/reflexion
- 复现数字：HumanEval Python 前 20 题，simple baseline vs Reflexion 的 pass@1

## 结果

| 策略 | pass@1 |
|---|---|
| simple (baseline) | 19/20 = 95.0% |
| reflexion | 19/20 = 95.0% |

> 在 deepseek-chat 上两者无差异，原因分析见 REPORT.md。

## 怎么跑

```bash
cd ~/repos/reflexion/programming_runs
head -20 benchmarks/humaneval-py.jsonl > /tmp/twenty.jsonl

# baseline
.venv/bin/python main.py --run_name hf20_simple --root_dir root \
  --dataset_path /tmp/twenty.jsonl --strategy simple --language py \
  --model deepseek-chat --pass_at_k 1 --max_iters 1 --verbose

# reflexion
.venv/bin/python main.py --run_name hf20_reflexion --root_dir root \
  --dataset_path /tmp/twenty.jsonl --strategy reflexion --language py \
  --model deepseek-chat --pass_at_k 1 --max_iters 3 --verbose
```

## 环境

- WSL2 Ubuntu
- Python 3.11（uv venv）
- openai 0.27.x（老版 SDK）
- DeepSeek 官方 API（deepseek-chat）

## 文档索引

- REPORT.md — 复现结果、归因与负结果分析
- debug_log.md — 环境与实现 bug 记录
- agent_log.md — 与 AI 协作过程记录
- docs/paper_notes.md — 论文核心模块精读