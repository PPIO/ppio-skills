# LLM API 指南

PPIO 提供兼容 OpenAI 的 API，模型目录持续更新。

最后验证：2026-03-12

## 目录
- [快速配置](#快速配置)
- [基础聊天补全](#基础聊天补全)
- [关键参数](#关键参数)
- [函数调用（工具使用）](#函数调用工具使用)
- [视觉（图像输入）](#视觉图像输入)
- [结构化输出（JSON 模式）](#结构化输出json-模式)
- [批量 API](#批量-api)
- [高级功能](#高级功能)
- [API 参考](#api-参考)

## 快速配置

```python
from openai import OpenAI

client = OpenAI(
    base_url="https://api.ppio.com/openai",
    api_key="<YOUR_API_KEY>",
)
```

## 基础聊天补全

```python
response = client.chat.completions.create(
    model="deepseek/deepseek-r1",
    messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "Hello!"}
    ],
    max_tokens=512,
    stream=True,  # 长响应推荐开启流式
)

for chunk in response:
    print(chunk.choices[0].delta.content or "", end="")
```

## 关键参数

### 模型选择
- 浏览模型：https://ppio.com/ai-computing/llm-api
- 通过 API 查询：`GET https://api.ppio.com/openai/v1/models`
- 格式：`provider/model-name`（例如 `deepseek/deepseek-r1`）
- 获取最新模型列表：
```bash
curl https://api.ppio.com/openai/v1/models \
  -H "Authorization: Bearer $PPIO_API_KEY"
```

### 输出控制

| 参数 | 说明 | 典型值 |
|-----------|-------------|---------------|
| `max_tokens` | 最大响应长度 | 512-4096 |
| `temperature` | 创造性（0=确定性，2=高创造性） | 0.7 |
| `top_p` | 核采样 | 0.9 |
| `stream` | 流式返回响应块 | true |

### 重复控制

| 参数 | 说明 |
|-----------|-------------|
| `presence_penalty` | 对已出现的 token 惩罚（鼓励引入新话题） |
| `frequency_penalty` | 根据频率惩罚（减少重复） |
| `stop` | 停止生成的终止序列 |

---

## 函数调用（工具使用）

让 LLM 调用外部函数/API。

### 支持的模型
- `minimax/minimax-m2.5`
- `qwen/qwen3-coder-next`
- `zai-org/glm-4.7-flash`
- [查看全部](https://ppio.com/ai-computing/llm-api)

### 示例

定义工具，携带 `tools` 参数调用模型，然后读取返回的工具调用信息。
```python
import json

tools = [
    {
        "type": "function",
        "function": {
            "name": "get_weather",
            "description": "Get weather for a location",
            "parameters": {
                "type": "object",
                "properties": {
                    "location": {
                        "type": "string",
                        "description": "City and state, e.g. San Francisco, CA"
                    }
                },
                "required": ["location"]
            }
        }
    }
]

response = client.chat.completions.create(
    model="deepseek/deepseek-v3.2",
    messages=[{"role": "user", "content": "What's the weather in Tokyo?"}],
    tools=tools,
)

tool_call = response.choices[0].message.tool_calls[0]
print(tool_call.function.name)       # "get_weather"
print(tool_call.function.arguments)  # '{"location": "Tokyo, Japan"}'
```

---

## 视觉（图像输入）

使用视觉语言模型处理图像。

### 支持的模型
- `qwen/qwen2-vl-72b-instruct`
- `meta-llama/llama-4-maverick`
- [查看全部视觉模型](https://ppio.com/ai-computing/llm-api)

### 通过 URL 传入图像

```python
response = client.chat.completions.create(
    model="qwen/qwen2-vl-72b-instruct",
    messages=[
        {
            "role": "user",
            "content": [
                {
                    "type": "image_url",
                    "image_url": {
                        "url": "https://example.com/image.jpg",
                        "detail": "high"  # high、low 或 auto
                    }
                },
                {"type": "text", "text": "Describe this image."}
            ]
        }
    ],
)
```

### 通过 Base64 传入图像

```python
import base64

with open("image.jpg", "rb") as f:
    base64_image = base64.b64encode(f.read()).decode("utf-8")

response = client.chat.completions.create(
    model="qwen/qwen2-vl-72b-instruct",
    messages=[
        {
            "role": "user",
            "content": [
                {
                    "type": "image_url",
                    "image_url": {
                        "url": f"data:image/jpeg;base64,{base64_image}",
                        "detail": "high"
                    }
                },
                {"type": "text", "text": "What text is in this image?"}
            ]
        }
    ],
)
```

---

## 结构化输出（JSON 模式）

强制 LLM 输出符合指定 Schema 的有效 JSON。

### 示例

```python
response = client.chat.completions.create(
    model="deepseek/deepseek-v3.2",
    messages=[
        {"role": "system", "content": "Extract expense info as JSON."},
        {"role": "user", "content": "I spent $50 on lunch and $30 on coffee today."}
    ],
    response_format={
        "type": "json_schema",
        "json_schema": {
            "name": "expenses",
            "schema": {
                "type": "object",
                "properties": {
                    "items": {
                        "type": "array",
                        "items": {
                            "type": "object",
                            "properties": {
                                "description": {"type": "string"},
                                "amount": {"type": "number"},
                                "category": {"type": "string"}
                            },
                            "required": ["description", "amount"]
                        }
                    }
                },
                "required": ["items"]
            }
        }
    }
)
```

---

## 批量推理 API

以折扣价格异步处理大批量请求（当前条款请查看实时定价/文档）。

### 工作流
1. 上传包含请求的 JSONL 文件
2. 创建批量任务
3. 轮询任务状态
4. 下载结果

```python
with open("requests.jsonl", "rb") as f:
    file = client.files.create(file=f, purpose="batch")

batch = client.batches.create(
    input_file_id=file.id,
    endpoint="/v1/chat/completions",
    completion_window="24h"
)

status = client.batches.retrieve(batch.id)
print(status.status)  # "completed"

results = client.files.content(status.output_file_id)
```
具体文档：https://ppio.com/docs/model/inference


---

## 高级功能

### 提示缓存
自动缓存重复的提示前缀，加快响应速度并降低成本。

### 推理模型
`deepseek/deepseek-r1` 等模型会输出推理过程：
```python
print(response.choices[0].message.reasoning_content)
```

### 流式输出
长内容输出时始终使用流式模式以避免超时：
```python
stream = client.chat.completions.create(
    model="deepseek/deepseek-r1",
    messages=[...],
    stream=True,
)
for chunk in stream:
    print(chunk.choices[0].delta.content or "", end="")
```

---

## API 参考

- **endpoint**：`POST https://api.ppio.com/openai/v1/chat/completions`
- **完整 API 文档**：https://ppio.com/docs/models/reference-llm-create-chat-completion
- **限流**：根据账户等级和模型有所不同，请在文档/控制台中查看当前限制
