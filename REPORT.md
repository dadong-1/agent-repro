# Reflexion 复现报告

## 复现目标

- 论文：Reflexion: Language Agents with Verbal Reinforcement Learning (NeurIPS'23)
- 仓库：noahshinn/reflexion
- 复现数字：HumanEval Python 上 simple baseline vs Reflexion 的 pass@1

## 实验设置

- 数据集：HumanEval Python，前 20 题（官方 humaneval-py.jsonl）
- 模型：deepseek-chat（DeepSeek 官方 API）
- temperature：0
- simple：max_iters=1
- reflexion：max_iters=3
- pass@k：1

## 结果

| 策略 | pass@1 |
|---|---|
| simple (baseline) | 19/20 = 95.0% |
| reflexion | 19/20 = 95.0% |

## Claim 对比

- 论文 Claim：Reflexion 通过自我反思提升代码生成准确率
- 论文报告：HumanEval 上 reflexion 优于 simple baseline（具体数字以论文正文为准）
- 我实测：95.0% vs 95.0%，无差异

## 我排除的

- 数据：官方 humaneval-py.jsonl，前 20 题，格式正确
- 实现：reflexion.py 核心循环已读通，"第一次生成→测试→反思→再生成"流程确认正确
- 超参：temperature=0，max_iters=3，与论文设置一致
- 评测：用官方 human-eval 测试，is_solved 判定正确

## 我认为的原因

deepseek-chat 能力较强，HumanEval 前 20 题难度偏低，baseline 第一次就写对了 19 题。
Reflexion 的机制是"做错了才反思改"，第一次全对就没有发挥空间。
论文报告的提升是在 gpt-4 + 更难题目 + 更多迭代下测出来的。

## 这个 claim 在我的规模下

不可判定（在强模型 + 简单题条件下，Reflexion 增益被模型能力吃掉）。

## 下一步怎么验

- 换更弱的模型，让 baseline 准确率降下来，看 reflexion 是否有提升
- 选更难的 20 题（HumanEval 后 50 题）
- 增加 max_iters 到 10

## 我仍然不知道什么

1. deepseek-chat 与论文 gpt-4 的能力差距具体多少
2. HumanEval 前 20 题是否确实太简单
3. 反思文本质量如何影响最终结果