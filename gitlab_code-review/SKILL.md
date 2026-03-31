---
name: code-review
description: >
  Perform a thorough, structured code review on any file, directory, diff, or MR in this workspace.
  Use this skill whenever the user asks to "review", "check", "audit", or "look over" code — including
  phrases like "can you review this MR", "review my changes", "does this code look good", "what do you
  think of this implementation", "check for issues in X", or "audit my code". Also trigger when the user
  shares a diff or file path and asks for feedback. Works across all stacks: TypeScript/Node, Python/FastAPI,
  React, Python agent bots, Java/Spring Boot, and more.
---

# Code Review Skill

You are performing a code review. Your goal is to give the developer honest, prioritized, actionable
feedback — the kind a thoughtful senior engineer on their team would give.

## Step 1: Understand what to review

If the user hasn't already specified, determine:
- **What**: a file path, directory, git diff, MR description, or pasted code
- **Context**: is this a bug fix, new feature, refactor, or hotfix? (affects how strict to be)
- **Stack**: auto-detect from file extensions, imports, and config files (see Step 2)
- **GitLab MR?**: if the user mentions a MR number and project (e.g. "review MR #5 in myproject"), note the
  owner, project, and MR number — you'll post the review to GitLab automatically after completing it (see Step 5)

If the target is ambiguous, ask one brief clarifying question. If a file path is given, read it. If
a directory is given, list its contents and read the most relevant files (entry points, changed files,
core logic). For a git diff, run `git diff` or `git diff HEAD~1` as appropriate.

**For GitLab MRs:** use `gitlab_get_merge_request` to fetch MR metadata and
`gitlab_get_merge_request_files` to get the diff. The `patch` field on each file contains the
actual diff hunks — read these carefully before writing the review.

## Step 2: Auto-detect the stack

Look at file extensions, package managers, and imports:

| Signals | Stack | Reference file |
|---------|-------|----------------|
| `.ts`, `pnpm-lock.yaml`, `tsdown`, `vitest.config` | TypeScript / Node | `references/typescript-node.md` |
| `fastapi`, `uvicorn`, `pydantic`, `alembic` | Python / FastAPI backend | `references/python-fastapi.md` |
| `react`, `vite`, `tsx`, `tailwind` | React frontend | `references/react-frontend.md` |
| `temporal`, `uv.lock`, `telegram`, `langchain`, `openai` | Python agent / bot | `references/python-agents.md` |
| `spring`, `@SpringBootApplication`, `pom.xml`, `build.gradle` | Java / Spring Boot | `references/java-spring.md` |
| Mixed (e.g., FastAPI + React in same project) | Multi-stack | Read both relevant files |

Read the relevant reference file(s) before writing your review. They contain stack-specific heuristics,
common pitfalls, and checklist items that go beyond what's in this file.

## Step 3: Run the review

Evaluate the code across five dimensions. For each finding, assign a severity:
- 🔴 **Blocker** — must be fixed before merge/deploy; correctness, security, data integrity at risk
- 🟡 **Should-fix** — strong recommendation; may ship with a tracked follow-up
- 🔵 **Nice-to-have** — quality improvement, no obligation

### Dimensions

**1. Correctness**
- Does the code do what it's supposed to do?
- Are edge cases handled (null/None, empty inputs, 0, max values, concurrent access)?
- Are error paths handled gracefully — not silently swallowed?
- Are async operations awaited correctly?

**2. Security**
- No secrets, tokens, or credentials hardcoded or logged 🔴
- User input validated before use; no injection vectors 🔴
- New endpoints/routes are authenticated and authorized 🔴
- Sensitive data not exposed in error messages or logs 🟡

**3. Performance**
- No N+1 query patterns (ORM loops, missing eager loading) 🔴
- Large datasets are paginated or streamed; no unbounded fetches 🔴
- Expensive calls have timeouts; retries have backoff 🟡
- Resources (files, connections, streams) are closed properly 🔴

**4. Testing**
- New logic has tests — happy paths AND failure/edge cases
- Tests are readable, deterministic, and isolated
- Integration or E2E tests cover critical paths 🟡

**5. Maintainability**
- Names clearly express intent; no mystery abbreviations
- Functions have a single, clear responsibility
- No dead code, disabled tests, or TODO leftovers in production paths 🟡
- Architecture boundaries respected (no business logic leaking into controllers, routes, or UI)
- New config is externalized, not hardcoded

## Step 4: Write the review

Use this structure:

```
## Code Review: <file/PR/component name>
**Stack detected:** <stack>
**Change type:** <bug fix | new feature | refactor | other>

### Summary
<2–4 sentence high-level impression — be direct and honest>

### Findings

#### 🔴 Blockers
- **[File:line or area]** — <clear description of the problem and why it matters>
  ```suggestion
  // suggested fix if short and obvious
  ```

#### 🟡 Should-fix
- ...

#### 🔵 Nice-to-have
- ...

### What's working well
<1–3 things done right — skip if there's genuinely nothing worth calling out>

### Overall verdict
<Approve / Approve with minor comments / Request changes — with a one-sentence reason>
```

Keep the tone collegial and constructive. If the code is good, say so clearly. Don't pad the review
with minor style nits when there are real issues — prioritize signal.

If a single finding is complex, explain the *why*, not just the *what*. Developers learn better when
they understand the reasoning.

---

## Step 5: Post the review to GitLab (for GitLab MRs only)

After writing the review, always post it to the GitLab MR using
`gitlab_create_merge_request_note`. Do this automatically — don't ask the user, just post it.

### Choosing the right event type

| Situation | Event to use |
|-----------|--------------|
| No blockers, only minor comments or nothing | `APPROVE` |
| Blockers or should-fix items, MR author is **not** the authenticated user | `REQUEST_CHANGES` |
| Blockers or should-fix items, MR author **is** the authenticated user (own MR) | `COMMENT` |

GitLab does not allow a user to request changes on their own MR (returns a 422 error). If unsure
whether it's the user's own MR, try `REQUEST_CHANGES` first and fall back to `COMMENT` on a 422.

### Tool call

```
gitlab_create_merge_request_note(
  owner       = <project owner login>,
  project        = <project name>,
  merge_request_iid = <PR number as integer>,
  event       = "REQUEST_CHANGES" | "APPROVE" | "COMMENT",
  body        = <full review text from Step 4>
)
```

After a successful post, share the MR link with the user:
`https://gitlab.com/<owner>/<project>/pull/<number>`

---

## Reference files

Before writing the review, read the stack-specific reference file for deeper heuristics:

- TypeScript / Node → `references/typescript-node.md`
- Python / FastAPI backend → `references/python-fastapi.md`
- React frontend → `references/react-frontend.md`
- Python agents & bots → `references/python-agents.md`
- Java / Spring Boot → `references/java-spring.md`
