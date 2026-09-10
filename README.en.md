<div align="center">

<img src="assets/icon.svg" alt="OpenAI-Compatible API Proxy" width="120" />

### Turn any LLM API into an OpenAI-compatible endpoint in minutes

A lightweight compatibility layer for teams already relying on OpenAI SDKs: connect private models, third-party services, or an internal gateway without rewriting client code.

[![Quick Start](https://img.shields.io/badge/Quick%20Start-Local%20%7C%20Docker-55e5d5?style=flat)](#-quick-start)
[![Deployment Docs](https://img.shields.io/badge/Docs-Deployment%20Guide-50a6ff?style=flat)](docs/deployment.md)
[![Feedback](https://img.shields.io/badge/Feedback-Issues-9f86ff?style=flat)](https://github.com/Xplore-LAB/openai-compatible-proxy/issues)

[![GitHub stars](https://img.shields.io/github/stars/Xplore-LAB/openai-compatible-proxy?style=flat&label=stars&color=gold)](https://github.com/Xplore-LAB/openai-compatible-proxy/stargazers)
[![license](https://img.shields.io/badge/license-MIT-1683c4?style=flat)](LICENSE)
[![python](https://img.shields.io/badge/python-3.9%2B-32b643?style=flat)](requirements.txt)
[![stack](https://img.shields.io/badge/stack-FastAPI%20%2B%20Docker-7e35d5?style=flat)](Dockerfile)

[简体中文](README.md) · **English**

</div>

---

## ⚡ The 60-second tour

A lightweight compatibility proxy that wraps your upstream model endpoint behind an OpenAI-style `/v1/*` API, so existing SDKs, clients, and workflows can connect with minimal changes.

| What you want | What you get |
| --- | --- |
| Point existing OpenAI SDKs at another upstream | A `/v1/*` compatible proxy, clients only change `base_url` |
| Connect Open WebUI, Dify, or Cherry Studio to internal models | A stable OpenAI-style endpoint with Docker, Compose, and systemd deployment included |
| Switch model vendors without touching downstream | Upstream URL and model list configured via environment variables |

## 🎯 Why this exists

A lot of AI apps already know how to talk to the OpenAI API. The real challenge is preserving compatibility across SDKs, tools, deployments, and vendor changes.

This project helps you:

- keep an OpenAI-style API surface
- reduce downstream migration cost
- plug existing tools into a different upstream
- keep flexibility for private deployment, routing, and cost control

## ✨ Core features

- Exposes `GET /v1/models`
- Proxies `GET/POST /v1/{path}` to your upstream
- Passes through request headers safely
- Optionally forces `stream=true` for `chat/completions`
- Supports environment-based configuration
- Includes Docker, Compose, systemd, and usage examples

## 🚀 Quick start

### Local Python

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

## ⚙️ Configuration

Main environment variables:

- `REAL_BASE`: upstream OpenAI-compatible base URL
- `PROXY_MODELS`: comma-separated model ids exposed by `/v1/models`
- `FORCE_CHAT_STREAM`: force `stream=true` for chat completions
- `PROXY_TIMEOUT`: upstream timeout in seconds
- `PROXY_TITLE`: title shown on `/`

See `.env.example` for defaults.

## 🔌 API

### `GET /`

Returns basic metadata about the proxy.

### `GET /healthz`

Returns a simple health check payload.

### `GET /v1/models`

Returns a model list based on `PROXY_MODELS`.

### `GET/POST /v1/{path}`

Forwards requests to:

```text
{REAL_BASE}/{path}
```

## 🧪 Usage examples

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

### Compatible tools

- OpenAI Python SDK
- OpenAI Node SDK
- Cherry Studio
- Open WebUI
- Dify
- Any app that accepts `base_url` / `api_base`

## 🏗 Architecture

<p align="center">
  <img src="assets/banner.svg" alt="Architecture banner" width="100%" />
</p>

See [docs/architecture.md](docs/architecture.md) for the simplified request flow and positioning.

## 🗂 Project structure

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

## 📚 Documentation

- [docs/architecture.md](docs/architecture.md): architecture and request flow
- [docs/compatibility.md](docs/compatibility.md): compatibility notes
- [docs/deployment.md](docs/deployment.md): deployment guide
- [docs/faq.md](docs/faq.md): FAQ
- [docs/troubleshooting.md](docs/troubleshooting.md): troubleshooting
- [中文 README](README.md)

## 💡 Use cases

- Wrap a non-OpenAI upstream behind a familiar API
- Switch model vendors without changing downstream clients
- Add a thin compatibility layer for internal AI tools
- Provide one stable endpoint to multiple teams or apps

## 👤 Who should use this

- Developers who already rely on OpenAI SDKs
- Teams migrating away from a single model vendor
- Builders who want a simple compatibility layer before adopting a full AI gateway
- Anyone who needs a stable API surface for tools, automations, or internal platforms

## 🗺 Roadmap

See [ROADMAP.md](ROADMAP.md).

## 📝 Changelog

See [CHANGELOG.md](CHANGELOG.md).

## 📄 License

[MIT](LICENSE)
