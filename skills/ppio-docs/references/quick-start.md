# PPIO 快速入门

5 分钟快速上手 PPIO。

最后验证：2026-03-12

## 1. 获取 API Key

1. 登录 https://ppio.com（支持 手机号 / 账号密码 登录）
2. 进入 [Key 管理](https://ppio.com/settings/key-management)
3. 创建新的 API Key

## 2. 发起第一个 API 调用

PPIO **兼容 OpenAI SDK**，只需更改 Base URL。

### Python（OpenAI SDK）

```python
from openai import OpenAI

client = OpenAI(
    base_url="https://api.ppio.com/openai",
    api_key="<YOUR_API_KEY>",
)

response = client.chat.completions.create(
    model="deepseek/deepseek-r1",
    messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "Hello!"}
    ],
    max_tokens=512,
)
print(response.choices[0].message.content)
```

### cURL

```bash
curl https://api.ppio.com/openai/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $PPIO_API_KEY" \
  -d '{
    "model": "deepseek/deepseek-r1",
    "messages": [
      {"role": "system", "content": "You are a helpful assistant."},
      {"role": "user", "content": "Hello!"}
    ],
    "max_tokens": 512
  }'
```

对于其他 SDK/语言，基于以上 Python 示例和 OpenAI 兼容的 Base URL 生成等价代码。

## 3. 探索模型

- **浏览模型**：https://ppio.com/ai-computing/llm-api
- **通过 API 查询**：`GET https://api.ppio.com/openai/v1/models`

### 热门模型

模型可用性变化较快，请在 https://ppio.com/ai-computing/llm-api 确认最新情况。

| 模型 | 适用场景 |
|-------|----------|
| `deepseek/deepseek-r1` | 推理、编码 |
| `deepseek/deepseek-v3` | 通用 |
| `qwen/qwen3-235b-a22b` | 长上下文 |
| `meta-llama/llama-4-maverick` | 快速推理 |

## 4. 充值

新用户可获赠代金券。如需充值，访问[充值页面](https://ppio.com/billing)


## 下一步

- [LLM API 指南](llm-guide.md) — 函数调用、视觉等高级功能
- [沙箱指南](sandbox-guide.md) — 在云端沙箱中运行代码
- [GPU 指南](gpu-guide.md) — 独享 GPU 实例
- [集成指南](integrations.md) — 与 LangChain、Cursor 等工具集成
