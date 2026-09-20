## 模块：Reflexion 自我反思循环

论文原句：Reflexion enables an agent to learn from its own mistakes by generating free-text verbal feedback after each trial, which is stored in an episodic memory and used to guide subsequent trials.

我的翻译：Reflexion 让 agent 从错误中学习，每次失败后生成一段自然语言反思，存到记忆里，下一次尝试时带上这段反思。

输入 / 输出：
- in = 题目 prompt + 上次代码 + 测试反馈（报错）
- out = 新的代码实现

伪代码（我自己写的）：
  1. 生成第一版代码
  2. 跑内部测试
  3. 如果通过 → 用真实测试验证，结束
  4. 如果失败 → 生成一段反思（代码哪错了、该怎么改）
  5. 把反思 + 上次代码 + 报错一起喂给模型，重新生成
  6. 再跑内部测试
  7. 重复 4-6，直到通过或达到 max_iters

代码位置：programming_runs/reflexion.py:33-90
  - 第一次生成：line 40
  - 跑测试：line 43
  - 生成反思：line 59-60
  - 带反思再生成：line 64-71
  - 再测：line 76

我做的验证：跑了 20 题 simple vs reflexion，发现强模型下 baseline 就 95%，reflexion 没机会发挥。

我还不懂：
  1. 反思文本的质量怎么影响最终结果
  2. episodic memory 的存储和检索具体怎么实现的