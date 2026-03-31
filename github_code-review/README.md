# 🐙 GitHub Enterprise Code Review

> **An intelligent, context-aware AI agent integration for Claude Desktop that performs rigorous, multi-stack code reviews and seamlessly posts autonomous feedback directly to your Pull Requests.**

This repository contains the `github_code-review` plugin architecture. By leveraging the Model Context Protocol (MCP), Claude can autonomously read your local diffs, fetch active GitHub PR metadata, perform deep semantic code analysis, and directly publish structured reviews back to your GitHub repository.

---

## 🔥 Key Capabilities

- **Autonomous Integration:** Automatically fetches PR diffs and publishes formatted reviews directly as GitHub PR comments or Change Requests.
- **Intelligent Stack Detection:** Dynamically recognizes your repository's exact tech stack (TypeScript, Python, React, Java) and applies framework-specific best practices and heuristics.
- **Prioritized Triage:** Every review strictly categorizes issues by severity:
  - 🔴 **Blockers** (Security vulnerabilities, N+1 queries, race conditions)
  - 🟡 **Should-fix** (Maintainability issues, missing test coverage)
  - 🔵 **Nice-to-have** (Style nits, modern language features)

---

## 💬 Natural Language Activation

No complex CLI commands. Simply ask Claude in plain text:

- *"Review PR #45 in my-repo"*
- *"Review my uncommitted changes before I push to GitHub"*
- *"Check the auth module and execute a full security audit"*
- *"Does this React hook logic look sound?"*

---

## 🏗️ Model Context Protocol (MCP) Setup

To allow Claude to securely read your source code and post reviews on your behalf, you must attach the official GitHub MCP server to your local environment.

1. Generate a **GitHub Personal Access Token (PAT)** with **Pull requests: Read and write** permissions.
2. Inject it into your Claude Desktop configuration (`~/Library/Application Support/Claude/claude_desktop_config.json`):

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "<YOUR_SECURE_TOKEN>"
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
curl -L -o github_code-review.plugin https://github.com/Kibrom1/skills/releases/latest/download/github_code-review.plugin
```

### 2. Deploy to Claude
- **macOS / Windows**: Simply **Double-click** the local `github_code-review.plugin` file.
- **Alternative**: Drag and drop the `.plugin` file directly onto the Claude Desktop application window.

Claude will handle unpacking and integrating the skill module instantly. No secondary configurations are necessary.

---
*Enterprise-grade automated reviews. MIT Licensed.*
