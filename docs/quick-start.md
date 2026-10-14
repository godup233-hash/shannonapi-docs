# 快速开始与协议细节

## 双协议端点：路径规则相反

ShannonAPI 同时暴露两种协议，**Base URL 的写法相反**，这是绝大多数接入报错的根源：

| 客户端类型 | Base URL 正确写法 | 原因 |
| --- | --- | --- |
| Anthropic 原生客户端（Cline 的 Anthropic 模式、cherry-studio 的 Anthropic 类型、AstrBot 的 Anthropic 类型） | `https://shannonapi.xyz` | 客户端自动请求 `/v1/messages` |
| OpenAI 兼容客户端（绝大多数工具） | `https://shannonapi.xyz/v1` | 客户端自动请求 `/v1/chat/completions` |

口诀：**Anthropic 模式不带 `/v1`，OpenAI 模式必须带 `/v1`。**

## Anthropic Messages API

```
POST https://shannonapi.xyz/v1/messages
Headers:
  x-api-key: <your-key>
  anthropic-version: 2023-06-01
  content-type: application/json
```

支持能力：`stream: true` 流式输出、`tools` 工具调用（Tool Call）、`system` 系统提示词、`cache_control` Prompt Cache。

Prompt Cache 示例（把稳定的长前缀打上缓存，重复请求按缓存读取价计费）：

```bash
curl https://shannonapi.xyz/v1/messages \
  -H "x-api-key: $SHANNON_API_KEY" \
  -H "anthropic-version: 2023-06-01" \
  -H "content-type: application/json" \
  -d '{
    "model": "claude-sonnet-5-5",
    "max_tokens": 1024,
    "system": [
      {"type": "text", "text": "<你的长系统提示词/知识库>", "cache_control": {"type": "ephemeral"}}
    ],
    "messages": [{"role": "user", "content": "总结上文要点"}]
  }'
```

注意：`cache_control` 要打在**每次请求都不变的前缀**（system、长文档）上才有命中率；内容太短（几十 token）没有意义。

## OpenAI Chat Completions API

```
POST https://shannonapi.xyz/v1/chat/completions
Headers:
  Authorization: Bearer <your-key>
  content-type: application/json
```

支持能力：`stream`、`tools`（function calling）、`response_format` JSON mode（部分模型）、自动 Prompt Cache（相同前缀自动命中，无需手动标记）。

```bash
curl https://shannonapi.xyz/v1/chat/completions \
  -H "Authorization: Bearer $SHANNON_API_KEY" \
  -H "content-type: application/json" \
  -d '{
    "model": "gpt-5.6-sol",
    "stream": true,
    "messages": [{"role": "user", "content": "写一段快速排序"}]
  }'
```

## 错误码速查

| HTTP | 含义 | 处理 |
| --- | --- | --- |
| 401 / 403 | Key 无效、过期或权限不足 | 回控制台重新创建 Key |
| 404 | Base URL 路径错误 | 对照上表检查 `/v1` |
| 429 | 并发或渠道限流 | 降低客户端并发，稍后重试 |
| timeout / 5xx | 上游波动或长请求被切断 | 调大客户端超时后重试 |

完整排错见 [troubleshooting.md](troubleshooting.md)。
