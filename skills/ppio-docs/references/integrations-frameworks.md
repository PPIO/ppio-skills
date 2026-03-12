# 集成：框架

最后验证：2026-03-12

## 目录
- [LangChain（Python）](#langchain-python)
- [LlamaIndex](#llamaindex)
- [OpenAI Agents SDK](#openai-agents-sdk)

## LangChain（Python）

```python
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(
    base_url="https://api.ppio.com/openai",
    api_key="<YOUR_API_KEY>",
    model="deepseek/deepseek-r1",
)

response = llm.invoke("Hello!")
print(response.content)
```

对于其他 SDK/语言，基于以上 Python 示例和 OpenAI 兼容的 Base URL 生成等价代码。

## LlamaIndex

```python
from llama_index.llms.openai_like import OpenAILike

llm = OpenAILike(
    api_base="https://api.ppio.com/openai",
    api_key="<YOUR_API_KEY>",
    model="deepseek/deepseek-r1",
)
```

## OpenAI Agents SDK

```python
from agents import Agent
from openai import OpenAI

client = OpenAI(
    base_url="https://api.ppio.com/openai",
    api_key="<YOUR_API_KEY>",
)

agent = Agent(
    name="Assistant",
    model="deepseek/deepseek-r1",
)
```
