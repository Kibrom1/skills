# Python Agent & Bot Review Heuristics
*Applies to: crypto-trading-agents (Temporal + uv), Agentic_Job_Scout (Telegram bot), and any LLM agent/workflow project*

## Agent architecture
- [ ] Agent boundaries are clear — each agent has a single, well-defined role (Broker, Execution, Judge) 🟡
- [ ] Agent-to-agent communication uses well-typed interfaces — no raw dict passing between agents 🟡
- [ ] LLM calls have retry logic with exponential backoff — API rate limits and timeouts happen 🟡
- [ ] Prompts are externalized (not buried deep in code) so they're easy to iterate on 🔵

## Temporal workflows (crypto-trading-agents)
- [ ] Workflow code is deterministic — no `datetime.now()`, `random`, or I/O directly inside `@workflow.defn` 🔴
- [ ] Activities handle all non-deterministic operations (API calls, DB, time) 🔴
- [ ] Activities have `schedule_to_close_timeout` and `retry_policy` configured 🔴
- [ ] Workflow state changes are idempotent — safe to replay on failure 🔴
- [ ] Signals and queries are used correctly — signals change state, queries read it

## Telegram bot (Agentic_Job_Scout)
- [ ] Handlers are registered with error handling — unhandled exceptions crash the polling loop 🔴
- [ ] Long operations run in async tasks or a thread pool — not blocking the bot's event loop 🔴
- [ ] User IDs / chat IDs are validated before use — don't trust Telegram message fields blindly
- [ ] Rate limiting considered — repeated API calls from the bot respect Telegram's limits 🟡
- [ ] Bot token is loaded from environment, never hardcoded 🔴

## LLM / AI API usage
- [ ] API keys loaded from environment variables — never committed or logged 🔴
- [ ] Model names are configurable (env var or config), not hardcoded — makes model swaps easier 🔵
- [ ] Token limits and context window size are considered for prompt assembly 🟡
- [ ] LLM outputs are validated before being acted upon — don't trust JSON from LLMs without parsing 🔴
- [ ] Costs are considered — avoid calling expensive models in tight loops

## Async Python
- [ ] `asyncio.run()` is only called at the top level — not nested inside async code 🔴
- [ ] `async for` / `async with` used correctly for async iterators and context managers
- [ ] CPU-bound work (model inference, data processing) offloaded to `run_in_executor` 🟡
- [ ] `asyncio.gather` used with `return_exceptions=True` to prevent one failure killing all tasks 🟡

## Dependency management (uv)
- [ ] `uv.lock` is committed and up to date — reproducible installs 🔴
- [ ] `pyproject.toml` groups dev dependencies under `[dependency-groups]` — not in production deps
- [ ] Version pins are present for critical dependencies (especially LLM clients, Temporal SDK)

## Data handling
- [ ] Financial or trading data is treated as `Decimal`, not `float` — floating point errors in money 🔴
- [ ] External API responses are parsed into typed models (Pydantic/dataclass) before use 🟡
- [ ] Structured outputs from LLMs use `response_format={"type": "json_object"}` or tool calls for reliability

## Testing
- [ ] Temporal workflows are tested with `WorkflowEnvironment.start_time_skipping()` 🟡
- [ ] LLM calls are mocked in tests — don't hit real APIs in unit/integration tests 🔴
- [ ] Bot handlers tested without a live Telegram connection (use `Application.builder().token("fake").build()`)

## Common pitfalls
- Using `time.sleep()` in async code — use `asyncio.sleep()` 🔴
- Not closing `httpx.AsyncClient` / `aiohttp.ClientSession` — use async context managers 🔴
- Treating LLM `tool_calls` as guaranteed — the model may return plain text or refuse
- Storing conversation history in memory only — restarts lose context; persist to DB or Redis 🟡
