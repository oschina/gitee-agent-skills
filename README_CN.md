# Gitee Agent Skills

基于 [Gitee MCP Server](https://gitee.com/oschina/mcp-gitee) 的 Agent Skills 合集，覆盖日常开发中最常用的 Gitee 工作流，包括 PR Review、Issue 实现、版本发布等。可在任何支持 Agent Skills 的应用中使用。

[English](./README.md)

## Skills 列表

| Skill | 功能描述 |
|-------|----------|
| `review-pr` | 获取 PR 差异、分析代码质量，并自动发布结构化 Review 评论 |
| `create-pr` | 自动生成规范的 PR 标题与描述，并提交拉取请求 |
| `merge-pr-check` | 检查 PR 是否具备合并条件，确认后执行合并 |
| `implement-issue` | Issue → 需求分析 → 辅助编码 → 创建 PR → 关闭 Issue 完整闭环 |
| `triage-issues` | 批量整理开放 Issue，按优先级、类型、状态分类 |
| `close-issue-flow` | 确认修复已合并后，规范关闭 Issue 并发布说明评论 |
| `daily-digest` | 汇总未读通知、待处理 PR 和 Issue，生成每日工作摘要 |
| `create-release` | 从合并的 PR 历史生成 Changelog 并发布版本 |
| `repo-explorer` | 快速了解任意 Gitee 仓库的结构与核心内容 |
| `search-and-fork` | 在 Gitee 上搜索开源项目并 Fork 最合适的仓库 |

## 前置要求

### 1. Gitee 账号与访问令牌

所有 Skills 通过 Gitee MCP Server 与 Gitee API 通信，需要个人访问令牌。

前往此处获取：**https://gitee.com/profile/personal_access_tokens**

所需权限范围：`projects`、`issues`、`pull_requests`、`notifications`

### 2. 配置 Gitee MCP Server

选择以下任意一种方式进行配置：

**方式一：Remote MCP（推荐，无需安装）**

```json
{
  "mcpServers": {
    "gitee": {
      "url": "https://api.gitee.com/mcp",
      "headers": {
        "Authorization": "Bearer <你的个人访问令牌>"
      }
    }
  }
}
```

**方式二：npx**

```json
{
  "mcpServers": {
    "gitee": {
      "command": "npx",
      "args": ["-y", "@gitee/mcp-gitee@latest"],
      "env": {
        "GITEE_ACCESS_TOKEN": "<你的个人访问令牌>"
      }
    }
  }
}
```

**方式三：本地可执行文件**

先安装：
```bash
go install gitee.com/oschina/mcp-gitee@latest
```

```json
{
  "mcpServers": {
    "gitee": {
      "command": "mcp-gitee",
      "env": {
        "GITEE_ACCESS_TOKEN": "<你的个人访问令牌>"
      }
    }
  }
}
```

## 安装方式

### OpenClaw

> **prereqs：** 推荐先在 OpenClaw 中安装 [mcporter skill](https://playbooks.com/skills/openclaw/skills/mcporter)。mcporter 是 OpenClaw 统一管理 MCP 服务的工具。

**第一步：安装 Skills**

将本仓库克隆到 OpenClaw Skills 目录：

```bash
git clone https://gitee.com/oschina/gitee-agent-skills ~/.openclaw/skills/gitee-agent-skills
```

**第二步：注册 Skills**

在 `~/.openclaw/openclaw.json` 中添加以下配置：

```json
{
  "skills": {
    "gitee-agent-skills": {
      "path": "~/.openclaw/skills/gitee-agent-skills/skills"
    }
  }
}
```

**第三步：通过 mcporter 添加 Gitee MCP Server**

OpenClaw 通过 [mcporter](https://github.com/steipete/mcporter) 统一管理 MCP 服务。执行以下命令注册 Gitee MCP Server：

```bash
# Remote MCP（推荐）
mcporter config add gitee \
  --url https://api.gitee.com/mcp \
  --header "Authorization: Bearer <你的个人访问令牌>"

# 或通过 npx
mcporter config add gitee \
  --command "npx -y @gitee/mcp-gitee@latest" \
  --env GITEE_ACCESS_TOKEN=<你的个人访问令牌>
```

验证注册成功：

```bash
mcporter list
```

**第四步：重启 OpenClaw**

Skills 将在下一次 Agent 会话时自动加载。

---

### Claude Code

**第一步：安装 Skills**

将本仓库克隆到 Claude Skills 目录：

```bash
# 全局安装（所有项目可用）
git clone https://gitee.com/oschina/gitee-agent-skills ~/.claude/skills/gitee-agent-skills

# 或仅对当前项目生效
git clone https://gitee.com/oschina/gitee-agent-skills .claude/skills/gitee-agent-skills
```

**第二步：配置 Gitee MCP Server**

将上方的 MCP Server 配置添加到 `~/.claude/mcp.json`。

**第三步：验证**

启动 Claude Code 后运行：

```
/mcp
```

确认 `gitee` 出现在已连接的服务器列表中。Skills 会被自动发现并加载。

## 使用示例

安装完成后，Skills 会根据你的请求自动激活，直接描述需求即可：

```
帮我 review owner/repo 的 PR #42
```
```
把 feature/login 分支合到 main，创建一个 PR
```
```
看看今天有哪些待处理的通知和 PR
```
```
帮我实现 owner/repo 的 Issue #15
```
```
给 owner/repo 发布 v1.3.0 版本
```
