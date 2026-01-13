---
title: Codex CLI 安装配置
tags:
  - vibe-coding
  - codex
  - openai
  - 安装
  - CLI
created: 2026-01-12
parent: "[[00 - Vibe Coding 指南首页]]"
---

# Codex CLI 安装配置

> [!info] 关于 Codex CLI
> Codex CLI 是 OpenAI 官方的命令行编程助手，支持代码生成、文件编辑、命令执行，并提供可配置的审批模式。

## 系统要求

| 系统 | 支持情况 |
|:---|:---|
| macOS | ✅ 完全支持 |
| Linux | ✅ 完全支持 |
| Windows | 实验性（建议 WSL） |

---

## 安装

### npm 安装

需要先完成 [[04 - 环境准备|环境准备]]。

```bash
npm install -g @openai/codex

# 验证安装
codex --version
```

### Homebrew 安装

```bash
brew install codex
```

---

## 认证配置

### 方式一：ChatGPT 订阅（==推荐==）

> [!success] 推荐原因
> - 使用 ChatGPT 账号登录即可
> - Plus/Pro/Business/Edu/Enterprise 计划包含 Codex CLI
> - 首次运行自动引导登录

```bash
# 首次运行会引导登录
codex
```

### 方式二：API Key

```bash
# 设置环境变量
export OPENAI_API_KEY="sk-xxxx"

# 永久保存
echo 'export OPENAI_API_KEY="sk-xxxx"' >> ~/.zshrc
source ~/.zshrc
```

---

## 基本使用

### 启动交互模式

```bash
codex
```

### 运行模式

- 交互式 TUI：`codex`
- 其他模式与参数请参考官方 CLI 文档（避免版本差异）

### 示例

```bash
# 启动交互模式
codex

# 在交互中输入任务，例如：
# "分析这个项目的代码结构"
```

---

## 内置命令

在交互模式中使用：

| 命令 | 功能 |
|:---|:---|
| `/model` | 切换模型（如 GPT-5-Codex / GPT-5） |
| `/help` | 查看帮助（其他命令以此为准） |

---

## 常用场景

### 代码审查

```
> 审查 src/components 目录下的 React 组件，找出潜在问题
```

### 重构代码

```
> 将 UserProfile 类组件重构为函数组件，使用 React Hooks
```

### 文档生成

```
> 为 api/ 目录下的所有接口生成 API 文档（Markdown格式）
```

### 测试编写

```
> 为 utils/date.ts 中的所有函数编写单元测试
```

### 批量修改

```
> 将所有 console.log 替换为项目的 logger 工具
```

---

## 高级功能

### 附加文件

```bash
# 附加截图让 Codex 分析
codex --attach screenshot.png "实现这个设计稿"

# 附加设计规范
codex --attach design-spec.pdf "按照规范实现登录页面"
```

### 网络搜索

Codex 可以搜索最新信息：

```
> 搜索 React 19 的新特性并总结
```

### MCP 支持

Codex 支持 MCP（参考官方文档）：
- https://developers.openai.com/codex/mcp

### 配置文件

配置项与格式请参考官方 CLI Reference：
- https://developers.openai.com/codex/cli/reference

---

## 安全与权限

> [!warning] 审批模式
> Codex CLI 支持不同审批模式；是否执行文件修改/命令由你确认。具体模式以官方文档为准。

### 审批级别

| 级别 | 说明 | 适用场景 |
|:---|:---|:---|
| `suggest` | 只显示建议 | 敏感项目 |
| `auto-edit` | 自动编辑文件，命令需确认 | 日常开发 |
| `full-auto` | 完全自动 | ==仅限信任的任务== |

---

## 与 Claude Code 对比

| 特性 | Codex CLI | Claude Code |
|:---|:---:|:---:|
| 提供商 | OpenAI | Anthropic |
| 认证方式 | ChatGPT 订阅 / API Key | Claude 订阅 / Console OAuth |
| MCP 支持 | ✅ | ✅ |
| 开源 | ✅（开源） | ❌ |

> [!tip] 选择建议
> - 已有 ChatGPT Plus → Codex CLI
> - 已有 Anthropic API → Claude Code
> - 都有 → 两个都试试，选择顺手的

---

## 故障排查

> [!question] Q: 提示登录失败
> **A:** 确认：
> 1. ChatGPT 订阅有效
> 2. 网络能访问 OpenAI
> 3. 尝试使用 API Key 方式

> [!question] Q: `command not found`
> **A:** 检查 npm 全局安装路径是否在 PATH 中

> [!question] Q: 沙箱限制太严格
> **A:** 在 config.toml 中配置允许的路径

---

## 快速参考

> [!important] 命令速查
> ```bash
> # 安装
> npm install -g @openai/codex
>
> # 启动
> codex
>
> # 运行
> codex
> 
> # 切换模型
> /model
> ```

---

## 参考资料

- [Codex CLI Overview](https://developers.openai.com/codex/cli)
- [Codex CLI Reference](https://developers.openai.com/codex/cli/reference)
- [Codex Pricing](https://developers.openai.com/codex/pricing)

---

**上一章**：← [[05 - Claude Code 安装配置]]
**下一章**：[[07 - MCP 配置指南]] →
