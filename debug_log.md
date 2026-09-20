# debug_log.md

每个 bug 一行，根因类别：环境/依赖、数据、实现、评测、理解错误。

| 日期 | 现象 | 根因类别 | 怎么发现 | 修法 | 怎么防止再犯 |
|---|---|---|---|---|---|
| 09-19 | openai 默认打 api.openai.com 报 401 | 环境/依赖 | 读 traceback 发现请求发到了 api.openai.com | utils.py 加 openai.api_base = os.getenv("OPENAI_API_BASE") | 复现前先确认 base_url 指向正确端点 |
| 09-19 | 模型名 gpt-4 在中转端点不存在 | 环境/依赖 | GET /models 返回的模型列表里没有 gpt-4 | 换成端点实际有的模型 | 跑前先 GET /models 确认模型名 |
| 09-19 | kimi-k2.7-code 报 "invalid temperature: only 1 is allowed" | 环境/依赖 | 跑 simple 时报 InvalidRequestError | 换成支持 temperature=0 的 deepseek-chat | 选模型前先 curl 测一次 temperature=0 是否接受 |
| 09-19 | AuthenticationError: api key invalid | 环境/依赖 | curl 直接调 /models 返回 401 | 换组里新的有效 key | 配 key 后先 curl 验证再跑实验 |