---
title: MCP 配置指南
tags:
  - vibe-coding
  - MCP
  - 配置
  - 进阶
created: 2026-01-12
parent: "[[00 - Vibe Coding 指南首页]]"
---

# MCP 配置指南

> [!info] 什么是 MCP
> **MCP (Model Context Protocol)** 是 Anthropic 于 2024-11-25 开源发布的开放标准，用于标准化 AI 系统与外部工具、数据源的连接方式。2025-12-09，Anthropic 将 MCP 捐赠给 Linux Foundation 的 Agentic AI Foundation (AAIF)。

## 核心概念

### MCP 是什么

```mermaid
flowchart LR
    AI[AI 助手] <--> |MCP 协议| S1[GitHub]
    AI <--> |MCP 协议| S2[Notion]
    AI <--> |MCP 协议| S3[数据库]
    AI <--> |MCP 协议| S4[文件系统]
    AI <--> |MCP 协议| S5[API 服务]
```

> [!tip] 类比理解
> MCP 就像 AI 世界的 ==USB-C 接口==：一次开发，到处使用。

### 三大组件

| 组件 | 说明 | 示例 |
|:---|:---|:---|
| **Resources** | AI 可以读取的数据 | 文件内容、数据库记录 |
| **Tools** | AI 可以执行的操作 | 创建 Issue、发送消息 |
| **Prompts** | 预设的指令模板 | 代码审查模板 |

### 传输类型

| 类型 | 说明 | 适用场景 |
|:---|:---|:---|
| `http` | HTTP 远程服务 | 云端 MCP 服务 |
| `sse` | Server-Sent Events | 实时更新 |
| `stdio` | 标准输入输出 | ==本地服务器== |

---

## Claude Code MCP 配置

### 查看当前状态

在 Claude Code 中运行：

```
/mcp
```

### 添加 MCP 服务器

#### 方式一：命令行添加

```bash
# HTTP 远程服务器
claude mcp add --transport http notion https://mcp.notion.com/mcp

# SSE 服务器
claude mcp add --transport sse asana https://mcp.asana.com/sse

# Stdio 本地服务器
claude mcp add --transport stdio github -- npx @modelcontextprotocol/server-github

# 带环境变量的服务器
claude mcp add --transport stdio \
  --env GITHUB_PERSONAL_ACCESS_TOKEN=ghp_xxx \
  github -- npx @modelcontextprotocol/server-github
```

#### 方式二：配置文件（==推荐==）

在项目根目录创建 `.mcp.json`：

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "ghp_your_token_here"
      }
    },
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem"],
      "env": {
        "ALLOWED_PATHS": "/Users/me/projects"
      }
    }
  }
}
```

---

## 常用 MCP 服务器

### GitHub

```bash
claude mcp add --transport stdio \
  --env GITHUB_PERSONAL_ACCESS_TOKEN=ghp_xxx \
  github -- npx @modelcontextprotocol/server-github
```

**获取 Token：**
1. GitHub → Settings → Developer settings → Personal access tokens
2. Generate new token (classic)
3. 选择需要的权限（repo, issues, pull_requests）

**可用功能：**
- 创建/查看 Issue
- 创建/审查 PR
- 搜索代码
- 管理仓库

### Filesystem

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem"],
      "env": {
        "ALLOWED_PATHS": "/Users/me/projects,/Users/me/documents"
      }
    }
  }
}
```

### PostgreSQL

```json
{
  "mcpServers": {
    "postgres": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres"],
      "env": {
        "DATABASE_URL": "postgresql://user:pass@localhost:5432/mydb"
      }
    }
  }
}
```

### 多服务器配置示例

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "ghp_xxx"
      }
    },
    "perplexity": {
      "command": "npx",
      "args": ["-y", "perplexity-mcp"],
      "env": {
        "PERPLEXITY_API_KEY": "pplx_xxx"
      }
    },
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem"],
      "env": {
        "ALLOWED_PATHS": "/Users/me/projects"
      }
    }
  }
}
```

---

## 配置作用域

```bash
# local（默认）：仅当前项目，仅自己可用
claude mcp add github --scope local

# project：整个项目共享（通过 .mcp.json）
claude mcp add github --scope project

# user：所有项目可用
claude mcp add github --scope user
```

| 作用域 | 存储位置 | 适用场景 |
|:---|:---|:---|
| `local` | 本地配置 | 个人实验 |
| `project` | `.mcp.json` | ==团队共享== |
| `user` | `~/.claude/` | 通用服务 |

---

## 管理命令

```bash
# 列出所有服务器
claude mcp list

# 查看服务器详情
claude mcp get github

# 移除服务器
claude mcp remove github

# 调试模式启动
claude --mcp-debug
```

---

## 在 Claude Code 中使用

配置完成后，Claude 会自动识别可用的 MCP 工具：

```
> 创建一个 GitHub Issue，标题为"修复登录bug"

> 搜索项目中所有包含 TODO 的文件

> 查询数据库中最近的用户注册记录
```

---

## 故障排查

### 诊断步骤

1. **检查状态**
   ```
   /mcp
   ```
   查看各服务器是否显示 "connected"

2. **启用调试模式**
   ```bash
   claude --mcp-debug
   ```

3. **检查环境变量**
   ```bash
   echo $GITHUB_PERSONAL_ACCESS_TOKEN
   ```

4. **测试服务器连接**
   ```bash
   claude mcp get github
   ```

### 常见问题

> [!question] Q: 服务器显示 "failed"
> **A:** 检查：
> 1. 环境变量是否正确设置
> 2. Token 是否有效
> 3. 网络是否能访问服务

> [!question] Q: npx 找不到包
> **A:** 确保 Node.js 环境正确，尝试：
> ```bash
> npx -y @modelcontextprotocol/server-github --help
> ```

> [!question] Q: 权限不足
> **A:** 检查 Token 的权限设置，确保包含所需的 scope

---

## 安全注意事项

> [!danger] 安全警告
> - **不要** 将 Token 提交到代码仓库
> - **不要** 在 `.mcp.json` 中硬编码敏感信息
> - **使用** 环境变量管理密钥
> - **定期** 轮换 Token

### 推荐做法

```bash
# 1. 使用环境变量
export GITHUB_PERSONAL_ACCESS_TOKEN="ghp_xxx"

# 2. 在配置中引用环境变量
# .mcp.json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "${GITHUB_PERSONAL_ACCESS_TOKEN}"
      }
    }
  }
}

# 3. 将 .mcp.json 加入 .gitignore（如果包含敏感配置）
```

---

## 参考资源

- [MCP 官方文档](https://modelcontextprotocol.io)
- [MCP GitHub](https://github.com/modelcontextprotocol)
- [Claude Code MCP 文档](https://code.claude.com/docs/en/mcp)
- [Introducing the Model Context Protocol - Anthropic](https://www.anthropic.com/news/model-context-protocol)
- [Donating the Model Context Protocol - Anthropic](https://www.anthropic.com/news/donating-the-model-context-protocol-and-establishing-of-the-agentic-ai-foundation)
- [Linux Foundation AAIF announcement](https://www.linuxfoundation.org/press/linux-foundation-announces-the-formation-of-the-agentic-ai-foundation)

---

**上一章**：← [[06 - Codex CLI 安装配置]]
**下一章**：[[08 - IDE工具详解]] →
