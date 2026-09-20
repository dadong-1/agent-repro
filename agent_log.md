# Agent 复现周记 · 工作日志（agent_log.md）

## 2026-09-19（Day 2）：首次运行实验 & API key 配置

### 今日目标
- 完成 Reflexion HumanEval 复现的首次端到端运行（先跑 1 条数据验证）

### 进展
- [x] 选定论文：Reflexion (NeurIPS'23)，代码仓库 noahshinn/reflexion
- [x] 完成代码精读（主循环 reflexion.py / 生成器 py_generate.py / 执行器 py_executor.py）
- [x] 搭建 Python 3.11 虚拟环境（uv）+ 安装最小依赖（openai==0.27.0 等）
- [x] API key 配置到环境变量（~/.bashrc），未写入任何代码文件
- [x] 1 条数据端到端跑通（simple baseline, is_solved=True）

### 遇到的问题与解决

1. **报错：openai.error.AuthenticationError: No API key provided**
   - 现象：数据加载、prompt 构建正常，发起 OpenAI 调用前直接失败
   - 原因：Reflexion 代码（openai 0.27）只从环境变量 OPENAI_API_KEY 读 key
   - 解决：把 key 写入 WSL ~/.bashrc，重开终端后自动加载
   - 经验：配置环境变量后必须新开终端；跑实验前先 source .venv/bin/activate

2. **Agent 误导：建议用 gpt-4 模型**
   - Agent 按论文默认配置建议 `--model gpt-4`，但组里给的 API 端点没有 gpt-4
   - 我自己 GET /models 才发现可用模型是 kimi/glm/deepseek 等
   - 教训：Agent 给的模型名要跟实际端点核对，不能照抄论文

3. **Agent 误导：建议换 base 到 api.deepseek.com**
   - 我一开始按 Agent 建议把 base 改成 api.deepseek.com，但旧 key 在那边无效
   - 最后确认是 key 本身过期，换了组里新 key 才通
   - 教训：base 改对了还要确认 key 对，两者独立验证

### 安全纪律
- key 只存环境变量，不写进任何代码/配置文件，不 push 到 GitHub

---

## 2026-09-20（Day 3）：20 题正式复现

### 进展
- [x] 跑了 20 题 simple baseline：19/20 = 95.0%
- [x] 跑了 20 题 reflexion (max_iters=3)：19/20 = 95.0%
- [x] 发现两者无差异，确认是强模型下简单题太简单导致
- [x] 写好 REPORT.md / debug_log.md / paper_notes.md

### Agent 协作
- Agent 帮我定位了 4 个配置坑（api_base、模型名、temperature、key）
- Agent 误导过 2 次（模型名、base url），我自己 curl 验证后纠正