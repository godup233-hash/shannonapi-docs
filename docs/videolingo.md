# VideoLingo 接入 ShannonAPI

VideoLingo 通过 OpenAI 兼容接口调用 LLM，只需修改 `config.yaml` 的 `api` 段。

```yaml
api:
  key: 'sk-你的Key'
  base_url: 'https://shannonapi.xyz/v1'
  model: 'claude-sonnet-5-5'
  llm_support_json: false
max_workers: 4
```

要点：

- `base_url` 填到 `/v1` 为止，VideoLingo 会自动拼接 `/chat/completions`；多写一段路径会 404。
- 模型推荐：翻译质量优先 `claude-sonnet-5-5`；成本优先 `deepseek-v4-flash` / `gpt-5.6-luna`。VideoLingo 的翻译链路涉及多轮结构化 JSON 输出，不建议用太小的模型。
- `max_workers` 是 LLM 并发数，默认 4。遇到 429 或 timeout 时降到 1–2。
- 也可以在 Streamlit 界面侧边栏的 **LLM Configuration** 里直接填 base_url / key / model，点 📡 按钮测试连通性。
- 长视频任务中途失败可直接重跑，VideoLingo 有断点续跑机制，不会重复翻译已完成的部分。

## 排错

| 现象 | 处理 |
| --- | --- |
| 启动即报连接错误 | 检查 `base_url` 是否 `https://shannonapi.xyz/v1`（带 `/v1`，不多不少） |
| 翻译中途 timeout | `max_workers` 降到 1–2；确认客户端所在网络到 `shannonapi.xyz` 连通（`curl -v https://shannonapi.xyz/v1/models`） |
| 429 | 降并发、稍后重跑 |
| 输出语言混乱 | 换 `claude-sonnet-5-5`；并在提示词里明确 target_language |
