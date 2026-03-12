# Agent 沙箱指南

PPIO Agent 沙箱是一个安全、隔离的云端环境，用于执行 AI 生成的代码。

最后验证：2026-03-12

## 目录
- [Agent 沙箱指南](#agent-沙箱指南)
  - [目录](#目录)
  - [功能特性](#功能特性)
  - [使用场景](#使用场景)
  - [快速入门](#快速入门)
    - [1. 安装 SDK](#1-安装-sdk)
    - [2. 配置 API Key](#2-配置-api-key)
    - [3. 创建并运行沙箱](#3-创建并运行沙箱)
  - [核心操作](#核心操作)
    - [运行代码](#运行代码)
    - [文件操作](#文件操作)
    - [后台命令](#后台命令)
  - [生命周期管理](#生命周期管理)
    - [暂停与恢复](#暂停与恢复)
    - [克隆](#克隆)
    - [空闲超时](#空闲超时)
  - [模板](#模板)
    - [创建模板](#创建模板)
    - [使用模板](#使用模板)
  - [CLI 使用](#cli-使用)
    - [安装 CLI](#安装-cli)
    - [命令](#命令)
  - [定价](#定价)
  - [框架集成](#框架集成)
    - [LangChain](#langchain)
    - [OpenAI Agents SDK](#openai-agents-sdk)
  - [E2B 兼容性](#e2b-兼容性)
  - [配额与模板限制](#配额与模板限制)
  - [相关资源](#相关资源)

## 功能特性

- **安全**：系统级隔离，保障代码安全执行
- **快速**：启动延迟取决于模板、区域和工作负载
- **多语言**：支持 Python、JavaScript、TypeScript、C++ 等
- **持久化**：支持暂停/恢复并保留状态
- **可扩展**：支持大规模并发沙箱

## 使用场景

- AI 数据分析与可视化
- AI Agent 的代码执行
- Computer Use Agent
- 安全的代码测试环境

---

## 快速入门

### 1. 安装 SDK

**JavaScript/TypeScript：**
```bash
npm i ppio-sandbox
```

**Python：**
```bash
pip install ppio-sandbox
```

### 2. 配置 API Key

```bash
export PPIO_API_KEY=sk_***
```

或在 `.env` 文件中：
```
PPIO_API_KEY=sk_***
```

### 3. 创建并运行沙箱

**Python（基础示例）：**
```python
# main.py
from dotenv import load_dotenv
from ppio_sandbox.code_interpreter import Sandbox


# .env 文件应位于项目根目录
# dotenv 会自动在当前工作目录查找 .env
load_dotenv()

# 或者
# 在命令行中设置环境变量
# export PPIO_API_KEY=sk_***

sandbox = Sandbox.create()
execution = sandbox.run_code("print('hello world')")
print(execution.logs)

files = sandbox.files.list("/")
print(files)

# 不再使用时关闭沙箱
sandbox.kill()
```

对于 JavaScript/TypeScript，基于此 Python 示例生成等价代码。

---

## 核心操作

以下 JavaScript 代码片段需在异步上下文中运行（ESM 顶层 await，或包裹在 `async function main()` 中）。

### 运行代码

```javascript
// JavaScript
const result = await sandbox.runCode(`
import pandas as pd
df = pd.DataFrame({'a': [1, 2, 3]})
print(df.describe())
`);
console.log(result.logs);
```

### 文件操作

```javascript
// 写入文件
await sandbox.files.write('/tmp/data.txt', 'Hello World');

// 读取文件
const content = await sandbox.files.read('/tmp/data.txt');

// 列出目录
const files = await sandbox.files.list('/tmp');

// 上传文件
await sandbox.files.upload('/local/file.txt', '/sandbox/file.txt');

// 下载文件
await sandbox.files.download('/sandbox/output.txt', '/local/output.txt');
```

### 后台命令

```javascript
// 在后台运行命令
const proc = await sandbox.commands.run('python long_task.py', {
  background: true
});

// 稍后检查状态
const status = await proc.status();

// 获取输出
const output = await proc.output();
```

---

## 生命周期管理

### 暂停与恢复

```javascript
// 暂停沙箱（保留状态）
await sandbox.pause();

// 稍后恢复
const resumed = await Sandbox.resume(sandbox.id);
```

### 克隆

```javascript
// 克隆沙箱
const clone = await sandbox.clone();
```

### 空闲超时

沙箱在空闲超时后自动暂停（当前默认值可能因产品策略和配置而变化）。

```javascript
// 长任务保持活跃
const sandbox = await Sandbox.create({
  keepAlive: true,
  timeout: 3600  // 1 小时
});
```

---

## 模板

创建带有预装包的可复用沙箱配置。

### 创建模板

```javascript
// 创建沙箱并安装包
const sandbox = await Sandbox.create();
await sandbox.runCode('pip install pandas numpy matplotlib');

// 保存为模板
const template = await sandbox.saveAsTemplate('data-science');
```

### 使用模板

```javascript
// 从模板创建沙箱
const sandbox = await Sandbox.create({
  template: 'data-science'
});
```

---

## CLI 使用

### 安装 CLI

```bash
npm i -g ppio-sandbox-cli
```

### 命令

按顺序执行以下命令：
```bash
ppio-sandbox auth login

ppio-sandbox spawn

ppio-sandbox list

ppio-sandbox shutdown <sandbox-id>
```

---

## 定价

- **CPU**：按秒计费
- **内存**：按秒计费
- **存储**：按天收费（模板、快照）

详情见 https://ppio.com/docs/guides/sandbox-pricing

---

## 框架集成

### LangChain

```python
from langchain_community.tools import PPIOSandboxTool

tool = PPIOSandboxTool()
result = tool.run("print('Hello from LangChain')")
```

### OpenAI Agents SDK

```python
from agents import Agent
from ppio_sandbox import Sandbox

sandbox = Sandbox()
agent = Agent(tools=[sandbox.as_tool()])
```

---

## E2B 兼容性

仅在用户将现有 E2B 工作流迁移到 PPIO 沙箱时使用。

- 兼容域名：`E2B_DOMAIN=sandbox.ppio.com`
- 鉴权变量：`E2B_API_KEY=<PPIO_API_KEY>`
- 推荐方案（获取完整功能）：PPIO 沙箱 SDK

最简 E2B Python 示例：
```python
from e2b_code_interpreter import Sandbox

sbx = Sandbox.create()
execution = sbx.commands.run("ls -l")
print(execution)
sbx.kill()
```

E2B CLI 流程：
```bash
e2b auth login
e2b sandbox spawn <template-id>
e2b sandbox list
e2b sandbox kill <sandbox-id>
```

---

## 配额与模板限制

当前策略要点（回答前请实时验证）：

- 并发沙箱配额可能受账户套餐限制（基准参考值为 100）。
- 模板 CPU 范围：`1-8 vCPU`（整数值）。
- 模板内存上限：`8192 MiB`，步长 `512 MiB`。
- CPU:内存比率规则：
  - 最小：`1:0.5`
  - 最大：`1:4`
- 模板限制在创建模板时生效，并由从该模板启动的沙箱继承。

如需确切当前限制或更高配额，请在给出具体数字前先查阅官方文档/联系支持。

---

## 相关资源

- **控制台**：https://ppio.com/sandbox/console
- **完整文档**：https://ppio.com/docs/sandbox/overview
- **SDK 参考**：https://ppio.com/docs/sandbox/sandbox-sdk-and-cli
- **E2B 兼容文档**：https://ppio.com/docs/sandbox/e2b-compatible
- **模板文档**：https://ppio.com/docs/sandbox/sandbox-template
- **文档索引**：https://ppio.com/docs/llms.txt
