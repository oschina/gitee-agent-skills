# Gitee Agent Skills

A collection of Agent Skills for [Gitee](https://gitee.com), built on top of the [Gitee MCP Server](https://gitee.com/oschina/mcp-gitee). These skills cover common developer workflows — reviewing PRs, implementing issues, publishing releases, and more — and can be used in any application that supports Agent Skills.

[中文文档](./README_CN.md)

## Available Skills

| Skill | Description |
|-------|-------------|
| `review-pr` | Review a PR: fetch diff, analyze code quality, and post a structured comment |
| `create-pr` | Create a well-structured PR with a generated title and description |
| `merge-pr-check` | Check if a PR is ready to merge, then execute the merge after confirmation |
| `implement-issue` | Full loop from Issue → requirements analysis → coding → PR → close |
| `triage-issues` | Batch-classify open issues by priority, type, and status |
| `close-issue-flow` | Close an issue with a proper comment after verifying the fix is merged |
| `daily-digest` | Aggregate notifications, pending PRs, and open issues into a daily summary |
| `create-release` | Generate a changelog from merged PRs and publish a release |
| `repo-explorer` | Generate a structured overview of any Gitee repository |
| `search-and-fork` | Search for open source projects on Gitee and fork the best match |

## Prerequisites

### 1. Gitee Account & Access Token

All skills communicate with Gitee via the Gitee MCP Server and require a personal access token.

Get yours at: **https://gitee.com/profile/personal_access_tokens**

Required scopes: `projects`, `issues`, `pull_requests`, `notifications`

### 2. Gitee MCP Server

Choose one of the following setup methods:

**Option A — Remote MCP (recommended, no installation)**

```json
{
  "mcpServers": {
    "gitee": {
      "url": "https://api.gitee.com/mcp",
      "headers": {
        "Authorization": "Bearer <your-personal-access-token>"
      }
    }
  }
}
```

**Option B — npx**

```json
{
  "mcpServers": {
    "gitee": {
      "command": "npx",
      "args": ["-y", "@gitee/mcp-gitee@latest"],
      "env": {
        "GITEE_ACCESS_TOKEN": "<your-personal-access-token>"
      }
    }
  }
}
```

**Option C — Local executable**

Install first:
```bash
go install gitee.com/oschina/mcp-gitee@latest
```

```json
{
  "mcpServers": {
    "gitee": {
      "command": "mcp-gitee",
      "env": {
        "GITEE_ACCESS_TOKEN": "<your-personal-access-token>"
      }
    }
  }
}
```

## Installation

### OpenClaw

> **prereqs:** It is recommended to install the [mcporter skill](https://playbooks.com/skills/openclaw/skills/mcporter) in OpenClaw first. mcporter is the unified MCP server manager for OpenClaw.

**Step 1 — Install the skills**

Clone this repository into your OpenClaw skills directory:

```bash
git clone https://gitee.com/oschina/gitee-agent-skills ~/.openclaw/skills/gitee-agent-skills
```

**Step 2 — Register the skills**

Add the following to `~/.openclaw/openclaw.json`:

```json
{
  "skills": {
    "gitee-agent-skills": {
      "path": "~/.openclaw/skills/gitee-agent-skills/skills"
    }
  }
}
```

**Step 3 — Add the Gitee MCP Server via mcporter**

OpenClaw manages MCP servers through [mcporter](https://github.com/steipete/mcporter). Run the following command to register the Gitee MCP Server:

```bash
# Remote MCP (recommended)
mcporter config add gitee \
  --url https://api.gitee.com/mcp \
  --header "Authorization: Bearer <your-personal-access-token>"

# Or via npx
mcporter config add gitee \
  --command "npx -y @gitee/mcp-gitee@latest" \
  --env GITEE_ACCESS_TOKEN=<your-personal-access-token>
```

Verify the server is registered:

```bash
mcporter list
```

**Step 4 — Restart OpenClaw**

The skills will be loaded on the next agent turn.

---

### Claude Code

**Step 1 — Install the skills**

Clone this repository into your Claude skills directory:

```bash
git clone https://gitee.com/oschina/gitee-agent-skills ~/.claude/skills/gitee-agent-skills
```

Or for a specific project only:

```bash
git clone https://gitee.com/oschina/gitee-agent-skills .claude/skills/gitee-agent-skills
```

**Step 2 — Configure the Gitee MCP Server**

Add the MCP server config to `~/.claude/mcp.json` using one of the options above.

**Step 3 — Verify**

Start Claude Code and run:

```
/mcp
```

Confirm `gitee` appears in the connected servers list. The skills will be automatically discovered and loaded.

## Usage

Once installed, the skills activate automatically based on your request. Just describe what you want to do:

```
Review PR #42 in owner/repo
```
```
Create a PR from feature/login to main
```
```
Show me today's pending notifications and open PRs
```
```
Implement issue #15 in owner/repo
```
```
Publish release v1.3.0 for owner/repo
```
