---
name: check-openai-compat
description: 拿到一个 OpenAI 兼容 API 端点后，用最小代价确认配置正确、能跑通第一个请求。
             当需要排查 base_url、模型名、temperature、API key 问题时使用。
---

## 什么时候用

- 跑某个用 openai SDK 的项目，报 401 / model not found / invalid temperature / 连不上
- 刚拿到一个新的 API key 或新的 base_url
- 不确定端点支持哪些模型

## 步骤（可以直接复制的命令）

1. **先确认 base_url 对不对**
   ```bash
   curl -sS -m 15 "$BASE_URL/models" -H "Authorization: Bearer $API_KEY" | head -c 500
   ```
   - 返回模型列表 → base 对、key 有效
   - 返回 401 → key 无效
   - 返回连接错误 → base 错或网络不通

2. **确认模型名在不在列表里**
   - 看 `/models` 返回的 `id` 字段，确认你要用的模型名存在
   - 不存在就换成列表里有的

3. **确认 temperature 支持**
   ```bash
   curl -sS -m 15 "$BASE_URL/chat/completions" \
     -H "Authorization: Bearer $API_KEY" -H "Content-Type: application/json" \
     -d '{"model":"<模型名>","messages":[{"role":"user","content":"ok"}],"max_tokens":10,"temperature":0}'
   ```
   - 200 → 支持 temperature=0
   - 报 "only 1 is allowed" → 这个模型是 reasoning 模型，要么换模型，要么把代码里 temperature 改成 1

4. **确认代码里有没有设 api_base**
   ```bash
   grep -rn "api_base" --include="*.py" .
   ```
   - 老版 openai SDK（<1.0）默认打 api.openai.com，必须手动设 `openai.api_base`
   - 只设 `OPENAI_API_BASE` 环境变量对老版 SDK 无效

5. **跑最小冒烟测试**
   - 1 条数据、1 次调用、max_iters=1
   - 端到端通了再放大

## 检查点：每一步怎么确认成功了

| 步骤 | 成功标志 |
|---|---|
| curl /models | 返回 JSON，里面有你要用的模型 id |
| curl chat/completions | 返回 200，有 choices |
| 代码 grep api_base | 找到 `openai.api_base = ...` 这一行 |
| 冒烟测试 | 写出结果文件，is_solved 有值 |

## 常见失败与处理

| 现象 | 原因 | 处理 |
|---|---|---|
| 401 Authentication Fails | key 无效 | 换 key，或确认是不是打错了服务器 |
| model not found | 模型名不在端点列表里 | GET /models 看实际有什么 |
| invalid temperature | reasoning 模型锁温度 | 换支持 temperature=0 的模型 |
| 默认打 api.openai.com | 老版 SDK 没设 api_base | 代码里加 `openai.api_base = os.getenv("OPENAI_API_BASE")` |
| 非交互 shell 读不到 key | .bashrc 有非交互 return | 脚本里显式 export，或自己终端跑 |

## 不要做什么

- 不要一上来就跑全量数据集，先跑 1 条
- 不要照抄论文里的模型名（gpt-4 之类），先 GET /models 确认
- 不要只设环境变量就指望老版 SDK 自动读