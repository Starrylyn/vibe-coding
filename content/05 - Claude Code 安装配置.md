---
title: Claude Code 安装配置
tags:
  - vibe-coding
  - claude-code
  - 安装
  - CLI
created: 2026-01-12
parent: "[[00 - Vibe Coding 指南首页]]"
---

# Claude Code 安装配置

> [!info] 关于 Claude Code
> Claude Code 是 Anthropic 官方的命令行 AI 编程工具，支持深度代码理解、自动执行任务、MCP 扩展等功能。

## 系统要求

| 系统 | 版本要求 |
|:---|:---|
| macOS | 10.15+ |
| Linux | Ubuntu 20.04+ / Debian 10+ |
| Windows | 10+ (通过 WSL) |
| RAM | 4GB+ 推荐 |
| 网络 | 需要互联网连接 |

---

## 安装方法

### 方法一：原生安装（==推荐==）

> [!success] 推荐原因
> - 无需 Node.js
> - 避免包管理器冲突
> - 最稳定

**macOS / Linux：**

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

**Windows (PowerShell)：**

```powershell
irm https://claude.ai/install.ps1 | iex
```

### 方法二：npm 安装（官方标注为 deprecated）

需要先完成 [[04 - 环境准备|环境准备]]。

```bash
# 全局安装
npm install -g @anthropic-ai/claude-code

# 验证安装
claude --version
```

### 其他安装方式（官方）

- Homebrew：`brew install --cask claude-code`
- WinGet：`winget install Anthropic.ClaudeCode`

---

## 认证配置

Claude Code 使用 Claude 账号登录（OAuth）。首次运行 `claude` 会引导登录。

### 个人用户

1. **Claude Pro / Max 订阅（推荐）**：使用 Claude.ai 账号登录即可
2. **Claude Console**：在 [console.anthropic.com](https://console.anthropic.com) 开通计费并完成 OAuth

### 团队 / 组织

- **Claude Team / Enterprise** 或 Claude Console 团队计费
- 可选通过云厂商（Bedrock / Vertex / Foundry）配置

> [!note] 说明
> Claude Code 使用账号登录，不依赖 `ANTHROPIC_API_KEY`（API Key 主要用于 Claude API）。

---

## 基本使用

### 启动

```bash
# 在项目目录中启动
cd /path/to/your/project
claude
```

### 内置命令

| 命令 | 功能 |
|:---|:---|
| `/help` | 查看帮助 |
| `/clear` | 清除对话历史 |
| `/compact` | 压缩上下文 |

### 命令行参数

```bash
# 添加额外目录
claude --add-dir ../apps ../lib

# 继续上次会话
claude -c

# 按会话 ID / 名称恢复
claude -r "session-id-or-name"

# 打印模式（不进入交互）
claude -p "解释这段代码"
```

---

## 常用场景示例

### 理解项目

```
> 帮我分析这个项目的整体架构
```

```
> 这个项目用了哪些技术栈？
```

### 修复 Bug

```
> src/api/user.ts 第 45 行报错 TypeError: Cannot read property 'id' of undefined，帮我分析原因并修复
```

### 添加功能

```
> 在用户列表页面添加搜索功能：
> - 支持按名字和邮箱搜索
> - 实时搜索（防抖）
> - 显示搜索结果数量
```

### 代码审查

```
> 审查 src/components/Login.tsx，找出潜在的安全问题和性能问题
```

### 生成测试

```
> 为 utils/validation.ts 中的所有函数编写单元测试，使用 Jest
```

### 重构代码

```
> 将 src/legacy/UserClass.js 重构为 TypeScript 函数组件
```

---

## 高级功能

### MCP 集成

详见 [[07 - MCP 配置指南]]

```bash
# 添加 MCP 服务器
claude mcp add github -- npx @modelcontextprotocol/server-github

# 查看状态
/mcp
```

### 自定义配置

创建 `~/.claude/config.json`：

```json
{
  "model": "claude-sonnet-4-20250514",
  "theme": "dark",
  "maxTokens": 8192
}
```

### 项目配置

在项目根目录创建 `CLAUDE.md`：

```markdown
# 项目说明

这是一个 React + TypeScript 项目。

## 代码规范
- 使用函数组件
- 使用 React Hooks
- 遵循 Airbnb 风格指南

## 目录结构
- src/components - React 组件
- src/hooks - 自定义 Hooks
- src/api - API 调用
```

---

## 故障排查

### 诊断工具

```bash
claude doctor
```

> [!tip] `claude doctor` 会自动检测
> - 安装是否正确
> - 认证是否有效
> - 配置是否正确
> - 常见问题的解决方案

### 常见问题

> [!question] Q: 安装后找不到 `claude` 命令
> **A:** 检查 PATH 配置：
> ```bash
> export PATH="$HOME/.claude/bin:$PATH"
> ```

> [!question] Q: 登录失败或权限不足
> **A:** 确认：
> 1. Claude 账号已订阅 Pro/Max 或 Console 计费已开启
> 2. 网络可访问 Claude 登录服务
> 3. 组织管理员未禁用 Claude Code

> [!question] Q: 连接超时
> **A:** 检查网络，确保能访问 `api.anthropic.com`

---

## 快速参考

> [!important] 命令速查
> ```bash
> # 安装
> curl -fsSL https://claude.ai/install.sh | bash
>
> # 登录（首次运行引导 OAuth）
> claude
> 
> # 启动
> cd your-project && claude
>
> # 诊断
> claude doctor
> ```

---

## 参考资料

- [Set up Claude Code](https://code.claude.com/docs/en/setup)
- [Claude Code CLI reference](https://code.claude.com/docs/en/cli-reference)

---

**上一章**：← [[04 - 环境准备]]
**下一章**：[[06 - Codex CLI 安装配置]] →
