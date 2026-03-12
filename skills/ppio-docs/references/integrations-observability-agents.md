# 集成：可观测性、Agent 与训练

最后验证：2026-03-12

## 目录
- [可观测性与代理](#可观测性与代理)
- [AI Agent](#ai-agent)
- [模型训练](#模型训练)

## 可观测性与代理

### LiteLLM（代理）

```python
import litellm

response = litellm.completion(
    model="deepseek/deepseek-r1",
    api_base="https://api.ppio.com/openai",
    api_key="<YOUR_API_KEY>",
    messages=[{"role": "user", "content": "Hello!"}]
)
```

### Helicone（日志）

```python
from openai import OpenAI

client = OpenAI(
    base_url="https://api.ppio.com/openai",
    api_key="<YOUR_API_KEY>",
    default_headers={
        "Helicone-Auth": "Bearer <HELICONE_KEY>",
    }
)
```

### Langfuse（链路追踪）

配置完成后，Langfuse 自动追踪兼容 OpenAI 的调用：
```python
from langfuse.openai import openai

openai.api_base = "https://api.ppio.com/openai"
openai.api_key = "<YOUR_API_KEY>"
```

### Portkey（网关）

```python
from portkey_ai import Portkey

client = Portkey(
    base_url="https://api.ppio.com/openai",
    api_key="<YOUR_API_KEY>",
    virtual_key="ppio-xxx"
)
```

## AI Agent

### Browser Use

```python
import asyncio
from browser_use import Agent
from langchain_openai import ChatOpenAI

async def main():
    llm = ChatOpenAI(
        base_url="https://api.ppio.com/openai",
        api_key="<YOUR_API_KEY>",
        model="deepseek/deepseek-r1",
    )

    agent = Agent(task="Search for...", llm=llm)
    await agent.run()

asyncio.run(main())
```

### Skyvern

在环境变量中设置：
```bash
LLM_API_BASE=https://api.ppio.com/openai
LLM_API_KEY=<YOUR_API_KEY>
MODEL_NAME=deepseek/deepseek-r1
```

## 模型训练

### Axolotl

在 Axolotl 配置文件中使用：
```yaml
base_model: ppio/model-name
api_url: https://api.ppio.com/openai
```

### Kohya SS GUI

在训练流水线中使用 PPIO 作为推理endpoint。
