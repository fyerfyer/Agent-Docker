[English](./README.md) | 中文

# agent-docker-mcp 🐳

> 本项目是 [Agent-sandbox-platform](https://github.com/fyerfyer/Agent-sandbox-platform) 的轻量级重构版本。

> **轻量级 Docker 沙箱 CLI，支持 MCP。** 专为 AI Agent（如 Cursor）打造，为它们提供安全、隔离且可随时销毁的环境来运行代码和命令。

[![npm version](https://img.shields.io/npm/v/agent-docker.svg)](https://www.npmjs.com/package/agent-docker)
[![License: Apache 2.0](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

在使用本地 AI 编码助手时，你通常希望它们能够运行测试、安装依赖或执行 shell 命令，而不会影响你的主机。`agent-docker-mcp` 可以无缝创建一个映射到你项目的临时 Docker 容器，并将其作为 MCP 服务器暴露给 AI 安全使用。

> ⚠️ **注意：** 本项目目前处于早期开发阶段，**仅在 Linux 系统和 Cursor IDE 上进行过测试**。macOS/Windows 支持以及与其他 MCP 客户端的兼容性可能有所不同。

## ✨ 特性

- **即时沙箱：** 立即启动一个隔离环境，完美映射到你当前的工作目录。
- **原生 MCP 支持：** 为 agent 暴露标准工具，安全地运行 bash 命令。
- **自动脚手架：** 自动设置 `.cursor/mcp.json` 或 `.vscode/mcp.json`，与你喜欢的 IDE 集成。
- **会话历史和回放：** 内置 SQLite 跟踪记录每个命令及其输出。你甚至可以像 asciinema cast 一样"回放"会话！

## 🚀 快速开始

**前置要求**：

- Node.js >= 18.0.0
- Docker Desktop / Engine 正在运行

你可以直接使用 `npx` 运行我们的 CLI（无需安装）：

```bash
# 1. 初始化沙箱并设置 agent 配置
npx agent-docker-mcp init

# 2. 为你的项目启动沙箱
npx agent-docker-mcp start
```

## 🛠 与 AI IDE 集成

### Cursor

通过在项目根目录运行 `npx agent-docker-mcp init`，CLI 会完成繁重的工作：

1. 检查 Docker 健康状态并拉取默认沙箱镜像。
2. 自动创建 `.cursor/mcp.json`（或 `.vscode/mcp.json`）以进行 MCP 集成。
3. 自动创建 `.cursorrules` 来指示 AI 优先使用沙箱而不是主机 shell。

然后你需要进入 `Settings` -> `Tools & MCP` 并启用 `agent-docker` MCP。

初始化完成后，AI agent 将自动访问 `agent-docker` MCP 工具！

### Copilot

只需在项目根目录运行 `npx agent-docker-mcp init`，一旦项目启动，Agent 将尝试自己启动 MCP 服务器！

## 📖 基本命令

```bash
# 初始化沙箱和脚手架
npx agent-docker-mcp init

# 为当前目录启动沙箱容器
npx agent-docker-mcp start

# 手动在沙箱内执行命令
npx agent-docker-mcp exec "npm run test"

# 列出活动的沙箱容器
npx agent-docker-mcp ps

# 停止当前沙箱
npx agent-docker-mcp stop

# 列出过去的沙箱会话
npx agent-docker-mcp history

# 回放特定的会话日志（非常适合调试 agent 操作）
npx agent-docker-mcp replay <session-id>

# 清理孤立的沙箱容器
npx agent-docker-mcp cleanup
```

## 🧠 工作原理

1. **身份挂载**：`agent-docker` 使用完全相同的路径将你的当前目录挂载到容器中（默认基于 `ubuntu`）。agent 生成或编辑的任何代码都会立即反映在你的主机上。
2. **MCP 服务器**：一旦生成标准配置（`init`），AI 会连接到 `npx agent-docker serve`，在容器内获取专用工具（`exec_bash` 等）。
3. **执行和审计**：命令通过 `dockerode` 库发送到 Docker。执行流被解析、安全地记录到本地 SQLite（`~/.agent-docker/db.sqlite`）中，并安全地返回给 AI。

## 📄 许可证

Apache 许可证。
