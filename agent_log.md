# Agent 复现周记 · 工作日志（agent_log.md）

## 2026-09-19（Day 2）：首次运行实验 & API key 配置

### 今日目标
- 完成 Reflexion HumanEval 复现的首次端到端运行（先跑 1 条数据验证）

### 进展
- [x] 选定论文：Reflexion (NeurIPS'23)，代码仓库 noahshinn/reflexion
- [x] 完成代码精读（主循环 reflexion.py / 生成器 py_generate.py / 执行器 py_executor.py）
- [x] 搭建 Python 3.11 虚拟环境（uv）+ 安装最小依赖（openai==0.27.0 等）
- [x] API key 配置到环境变量（~/.bashrc），未写入任何代码文件
- [ ] 1 条数据端到端跑通（进行中）

### 遇到的问题与解决

1. **报错：openai.error.AuthenticationError: No API key provided**
   - 现象：数据加载、prompt 构建正常，发起 OpenAI 调用前直接失败
   - 原因：Reflexion 代码（openai 0.27）只从环境变量 OPENAI_API_KEY 读 key；运行进程环境里没有该变量。ZCode 的免费额度只驱动 ZCode 工具本身，与实验代码的 API 调用相互独立。
   - 解决：把 key 写入 WSL ~/.bashrc（export OPENAI_API_KEY="..."），重开终端后自动加载；验证 `echo $OPENAI_API_KEY` 长度 67。
   - 经验：配置环境变量后必须新开终端（旧会话不会自动加载）；跑实验前先 `wsl` 进 Ubuntu、`source .venv/bin/activate`。

### 安全纪律
- key 只存环境变量，不写进任何代码/配置文件，不 push 到 GitHub（本次所有相关操作均已遵守）