# 🦊 GitLab Enterprise Code Review

> **An intelligent, context-aware AI agent integration for Claude Desktop that performs rigorous, multi-stack code reviews and seamlessly posts autonomous feedback directly to your Merge Requests.**

This repository contains the `gitlab_code-review` plugin architecture. By leveraging the Model Context Protocol (MCP), Claude can autonomously read your local diffs, fetch active GitLab MR metadata, perform deep semantic code analysis, and directly publish structured notes back to your GitLab project.

---

## 🔥 Key Capabilities

- **Autonomous Integration:** Automatically fetches MR diffs and publishes formatted reviews directly as GitLab MR notes.
- **Intelligent Stack Detection:** Dynamically recognizes your repository's exact tech stack (TypeScript, Python, React, Java) and applies framework-specific best practices and heuristics.
- **Prioritized Triage:** Every review strictly categorizes issues by severity:
  - 🔴 **Blockers** (Security vulnerabilities, N+1 queries, race conditions)
  - 🟡 **Should-fix** (Maintainability issues, missing test coverage)
  - 🔵 **Nice-to-have** (Style nits, modern language features)

---

## 💬 Natural Language Activation

No complex CLI commands. Simply ask Claude in plain text:

- *"Review MR #45 in my-project"*
- *"Review my uncommitted changes before I push to GitLab"*
- *"Check the auth module and execute a full security audit"*
- *"Does this React hook logic look sound?"*

---

## 🏗️ Model Context Protocol (MCP) Setup

To allow Claude to securely read your source code and post reviews on your behalf, you must attach the official GitLab MCP server to your local environment.

1. Generate a **GitLab Personal Access Token (PAT)** with the required API permissions (`api` scope).
2. Inject it into your Claude Desktop configuration (`~/Library/Application Support/Claude/claude_desktop_config.json`):

```json
{
  "mcpServers": {
    "gitlab": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-gitlab"],
      "env": {
        "GITLAB_PERSONAL_ACCESS_TOKEN": "<YOUR_SECURE_TOKEN>",
        "GITLAB_API_URL": "https://gitlab.com/api/v4"
      }
    }
  }
}
```
3. Restart your Claude Desktop client.

---

## 🚀 Easy Installation

### 1. Retrieve the Plugin
Clone the mono-repository or download the compiled asset directly from the Releases page:
```bash
curl -L -o gitlab_code-review.plugin https://gitlab.com/Kibrom1/skills/releases/latest/download/gitlab_code-review.plugin
```

### 2. Deploy to Claude
- **macOS / Windows**: Simply **Double-click** the local `gitlab_code-review.plugin` file.
- **Alternative**: Drag and drop the `.plugin` file directly onto the Claude Desktop application window.

Claude will handle unpacking and integrating the skill module instantly. No secondary configurations are necessary.

---
*Enterprise-grade automated reviews. MIT Licensed.*
