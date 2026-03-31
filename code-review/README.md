# Code Review Skill

An AI-powered code review skill for Claude Cowork that reviews code across multiple stacks and automatically posts reviews directly to GitHub PRs.

## What it does

- Reviews any file, directory, git diff, or GitHub PR
- Auto-detects the tech stack and applies the right heuristics
- Categorizes findings as 🔴 Blockers, 🟡 Should-fix, or 🔵 Nice-to-have
- Automatically posts the review as a comment on GitHub PRs

## Supported stacks

| Stack | What it checks |
|-------|----------------|
| TypeScript / Node | Type safety, async correctness, pnpm conventions |
| Python / FastAPI | SQLAlchemy, Pydantic v2, JWT auth, Alembic |
| React frontend | Hooks, TanStack Query, performance, security |
| Python agents & bots | Temporal workflows, Telegram bots, LLM API usage |
| Java / Spring Boot | Spring Security, JPA, transaction management |

## Usage

Just ask Claude naturally:

- *"Review PR #5 in my-repo"*
- *"Review my changes before I merge"*
- *"Check the auth module in brightsteps"*
- *"Does this code look good?"*

## GitHub PR integration

To post reviews directly to GitHub PRs, connect the `@modelcontextprotocol/server-github` MCP server:

1. Create a GitHub Personal Access Token with **Pull requests: Read and write** permission
2. Add to your Claude Desktop config (`~/Library/Application Support/Claude/claude_desktop_config.json`):

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "your-token-here"
      }
    }
  }
}
```

3. Restart Claude Desktop

## Installation

Double-click the `.plugin` file or drag it onto Claude Desktop.

## License

MIT
