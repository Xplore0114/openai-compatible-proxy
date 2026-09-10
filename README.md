<div align="center">

<img src="assets/icon.svg" alt="OpenAI 兼容代理" width="120" />

### 几分钟内,把任意大模型 API 变成 OpenAI 兼容接口

为已经依赖 OpenAI SDK 的团队准备的轻量兼容层:不改客户端代码,即可接入私有模型、第三方服务或内部统一出口

[![快速开始](https://img.shields.io/badge/快速开始-本地%20%7C%20Docker-55e5d5?style=flat)](#-快速开始)
[![部署文档](https://img.shields.io/badge/文档-部署指南-50a6ff?style=flat)](docs/deployment.md)
[![问题反馈](https://img.shields.io/badge/反馈-Issues-9f86ff?style=flat)](https://github.com/Xplore-LAB/openai-compatible-proxy/issues)

[![GitHub stars](https://img.shields.io/github/stars/Xplore-LAB/openai-compatible-proxy?style=flat&label=stars&color=gold)](https://github.com/Xplore-LAB/openai-compatible-proxy/stargazers)
[![license](https://img.shields.io/badge/license-MIT-1683c4?style=flat)](LICENSE)
[![python](https://img.shields.io/badge/python-3.9%2B-32b643?style=flat)](requirements.txt)
[![stack](https://img.shields.io/badge/stack-FastAPI%20%2B%20Docker-7e35d5?style=flat)](Dockerfile)

**简体中文** · [English](README.en.md)

</div>

---

## ⚡ 一分钟看懂 OpenAI 兼容代理

这是一个轻量代理层,把上游模型接口统一暴露为 OpenAI 风格的 `/v1/*` API,让现有 SDK、客户端和工作流系统快速接入。

| 你想完成的事 | 交付成果 |
| --- | --- |
| 让现有 OpenAI SDK 直接连别的上游 | `/v1/*` 兼容代理,客户端只需改 `base_url` |
| 给 Open WebUI、Dify、Cherry Studio 接内部模型 | 稳定的 OpenAI 风格端点,自带 Docker、Compose、systemd 部署 |
| 切换模型供应商但不改下游 | 环境变量配置上游地址与模型列表 |

## 🎯 它解决什么问题

很多 AI 工具已经默认支持 OpenAI API,但你的真实上游可能是别家的模型服务、自建网关,或者内部统一出口。

这个项目的价值在于:

- 不重写现有客户端
- 不改造现有 OpenAI 集成链路
- 用一层轻代理完成模型接入兼容
- 方便后续做切换、治理和控成本

## ✨ 核心能力

- 提供 `GET /v1/models`
- 转发 `GET/POST /v1/{path}` 到上游
- 透传大部分请求头,自动移除冲突头
- 可选强制 `chat/completions` 使用 `stream=true`
- 支持环境变量配置
- 自带 Docker、Compose、systemd 和使用示例

## 🚀 快速开始

### 本地运行

```bash
cp .env.example .env
pip install -r requirements.txt
export $(grep -v '^#' .env | xargs)
uvicorn proxy:app --host 0.0.0.0 --port 9000
```

### Docker

```bash
cp .env.example .env
docker build -t openai-compatible-proxy .
docker run --rm -p 9000:9000 --env-file .env openai-compatible-proxy
```

### Docker Compose

```bash
cp .env.example .env
docker compose up -d --build
```

## ⚙️ 核心配置

- `REAL_BASE`:上游 OpenAI-compatible 地址
- `PROXY_MODELS`:`/v1/models` 返回的模型列表
- `FORCE_CHAT_STREAM`:是否强制聊天接口流式返回
- `PROXY_TIMEOUT`:上游超时秒数
- `PROXY_TITLE`:根路径显示的服务标题

详情见 `.env.example`。

## 🔌 API

### `GET /`

返回代理的基础元信息。

### `GET /healthz`

返回简单的健康检查响应。

### `GET /v1/models`

按 `PROXY_MODELS` 返回模型列表。

### `GET/POST /v1/{path}`

将请求转发到:

```text
{REAL_BASE}/{path}
```

## 🧪 使用示例

### cURL

```bash
curl http://127.0.0.1:9000/v1/models
```

```bash
curl http://127.0.0.1:9000/v1/chat/completions \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer your-key' \
  -d '{
    "model": "gpt-4o-mini",
    "messages": [{"role": "user", "content": "hello"}]
  }'
```

### 适合接什么

- OpenAI Python SDK
- OpenAI Node SDK
- Cherry Studio
- Open WebUI
- Dify
- 任何支持 `base_url` / `api_base` 的客户端

## 🏗 架构说明

<p align="center">
  <img src="assets/banner.svg" alt="架构横幅" width="100%" />
</p>

可在 [docs/architecture.md](docs/architecture.md) 查看简化后的请求链路和定位说明。

## 🗂 项目结构

```text
.
├── assets/
│   └── icon.svg
├── proxy.py
├── requirements.txt
├── .env.example
├── Dockerfile
├── docker-compose.yml
├── docs/
│   ├── compatibility.md
│   ├── deployment.md
│   ├── faq.md
│   └── troubleshooting.md
└── examples/
    ├── python-openai-sdk/
    ├── cherry-studio/
    ├── open-webui/
    └── dify/
```

## 📚 文档导航

- [docs/architecture.md](docs/architecture.md):架构与请求链路
- [docs/compatibility.md](docs/compatibility.md):兼容性说明
- [docs/deployment.md](docs/deployment.md):部署指南
- [docs/faq.md](docs/faq.md):常见问题
- [docs/troubleshooting.md](docs/troubleshooting.md):排障手册
- [English README](README.en.md)

## 💡 适用场景

- 把非 OpenAI 上游包装成统一接口
- 给多个内部工具提供稳定的模型出口
- 在不改客户端的情况下切换模型供应商
- 做私有部署、统一网关或模型中转

## 👤 谁适合用

- 已经依赖 OpenAI SDK 的开发者
- 想摆脱单一模型供应商绑定的团队
- 想先上兼容层、后面再演进到完整 AI 网关的项目
- 需要给工具、自动化和内部平台提供稳定 API 的团队

## 🗺 路线图

见 [ROADMAP.md](ROADMAP.md)。

## 📝 更新记录

见 [CHANGELOG.md](CHANGELOG.md)。

## 📄 许可证

[MIT](LICENSE)
