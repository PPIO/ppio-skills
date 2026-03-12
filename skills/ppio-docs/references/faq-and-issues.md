# 常见问题与 FAQ

最后验证：2026-03-12

## 目录
- [API 问题](#api-问题)
- [计费问题](#计费问题)
- [GPU 实例问题](#gpu-实例问题)
- [Agent 沙箱问题](#沙箱问题)
- [集成问题](#集成问题)
- [获取帮助](#获取帮助)

## API 问题

### 鉴权失败
- 确保请求头格式正确：`Authorization: Bearer <API_KEY>`（不能只填 Key）
- 在 https://ppio.com/settings/key-management 检查 API Key 是否有效
- API Key 可能被禁用或删除，请确认状态

### 模型未找到
- 模型名称区分大小写
- 格式：`provider/model-name`（例如 `deepseek/deepseek-r1`）
- 查询可用 LLM(大语言) 模型：`GET https://api.ppio.com/openai/v1/models`

### 超出限流
- 限流根据账户等级和模型有所不同
- 调整客户端行为前请先在控制台/文档中确认当前限制
- 重试时使用指数退避策略
- 遵守 `429` 响应和 `Retry-After` 响应头
- 如需更高限额请联系支持（官网右下角联系入口）

### 请求超时
- 长输出请使用流式模式：`"stream": true`
- 如果响应过长，减小 `max_tokens`

## 计费问题

### 余额不足
- 在 https://ppio.com/billing 查看余额
- 账户充值与设置[余额不足提醒](https://ppio.com/docs/support/payment-methods)

### 付款失败
常见原因：
- 发卡机构拒绝（请联系银行）
- 卡片已过期或被冻结
- 卡内余额不足
- 风控拦截（尝试其他卡片）

## GPU 实例问题

### 实例无法重启
停止后资源可能被抢占。解决方法：
1. [保存实例镜像](https://ppio.com/docs/gpu/save-image)
2. 从保存的镜像创建新实例
3. 使用网络卷存储持久化数据

### 存储类型

| 类型 | 停止后保留 | 保存镜像后保留 | 挂载点 |
|------|------------------|------------------------|-------------|
| 容器磁盘 | 否 | 是 | `/` |
| 卷磁盘 | 否 | 否 | `/workspace` |
| 网络卷 | 是 | 独立 | `/network` |

### 查看 GPU 使用情况
```bash
pip install py3nvml
py3smi
```
（在容器中使用 `py3smi` 替代 `nvidia-smi`）

### CUDA 版本
CUDA 向后兼容。如需 CUDA 12.1，任何 >= 12.1 的版本均可使用。

## 沙箱问题

### 沙箱超时
- 空闲超时根据当前沙箱设置/策略有所不同
- 长任务请使用 `keep_alive=True`
- 考虑使用持久化/快照功能

### 命令执行失败
- 检查所需包是否已安装
- 验证文件路径是否正确
- 查看沙箱日志排查错误

## 集成问题

### OpenAI SDK 无法使用
使用以下基础客户端配置：
```python
client = OpenAI(
    base_url="https://api.ppio.com/openai",  # 注意：需要 /openai 后缀
    api_key="<YOUR_API_KEY>",
)
```

### LangChain 集成
```python
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(
    base_url="https://api.ppio.com/openai",
    api_key="<YOUR_API_KEY>",
    model="deepseek/deepseek-r1",
)
```

## 获取帮助
- **邮件**：support@ppio.com
- **FAQ**：
    - 模型 API 常见问题：https://ppio.com/docs/model/FAQs
    - GPU 容器常见问题：https://ppio.com/docs/gpu/faq
