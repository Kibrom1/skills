# ⚡ Enterprise AI Skills Suite

> **Accelerating engineering velocity through context-aware, autonomous AI workflows.**

This repository serves as the central hub for our custom, enterprise-grade AI skills built for Claude Desktop. By leveraging the **Model Context Protocol (MCP)**, these plugins integrate directly into our established VCS providers—providing automated, intelligent, and highly contextual code reviews without leaving the workspace.

---

## 🛠️ Integrated Capabilities

Our core focus is reducing PR/MR cycle times, catching security vectors early, and enforcing consistent engineering standards across the organization.

| Extension Name | Core Capability | Environment Target | Path |
| :--- | :--- | :--- | :--- |
| **GitHub Code Review** | Automated, multi-stack code reviews & inline commenting | GitHub Pull Requests | [`/github_code-review`](./github_code-review/) |
| **GitLab Code Review** | Automated, multi-stack code reviews & inline commenting | GitLab Merge Requests | [`/gitlab_code-review`](./gitlab_code-review/) |

### Supported Engineering Stacks
Both intelligent review skills automatically detect and adapt to our primary technology stacks using robust, heuristic-driven logic:
- **TypeScript / Node.js** (Type safety, async correctness, package management)
- **Python / FastAPI** (SQLAlchemy, Pydantic, route validation, Alembic)
- **React Frontend** (Hooks profiling, state management, security)
- **Java / Spring Boot** (Spring Security, JPA transaction boundaries)

---

## 🏗️ Architecture & Protocol Integration

These skills do not operate in a vacuum; they are built on standardized **Model Context Protocol (MCP)** servers.

1. **Context-Aware Reasoning:** The AI fetches exact git diffs and codebase context directly from the VCS provider before forming an opinion.
2. **Autonomous Execution:** The skill autonomously posts structured, categorized reviews (Blockers, Should-Fix, Nice-to-Have) directly into the PR/MR pipeline.
3. **Enterprise Security:** All API interactions occur locally on the engineer's machine using strict, scoped Personal Access Tokens (PATs). No source code is forwarded to unauthorized third-party logging servers.

---

## 🚀 Quick Deploy

To deploy a skill to your local Claude Desktop environment:

1. **Locate the Package:** Navigate into the respective skill directory. 
2. **Install:** Double-click the compiled `.plugin` file (e.g., `github_code-review.plugin`), or drag-and-drop it directly onto your Claude Desktop application icon.
3. **Authenticate:** Ensure your Claude configuration (`claude_desktop_config.json`) is properly populated with the requisite `@modelcontextprotocol/server-github` or `server-gitlab` environment tokens.

---

*Engineered to elevate global team standards and augment human review loops.*
