# Its The Lore

> **Give your coding agent the decisions your team already made — so it stops re-doing things you ruled out.**

Agents that know *why*. **Deterministic. Read-only. No RAG, no guessing.**

Lore keeps a team's recorded knowledge — requirements, decisions, designs,
roadmaps, and prompts — as typed Markdown in the repo, validates it in CI, and
serves it read-only to coding agents over MCP. Retrieval is deterministic and
reproducible: no embeddings, no model call to decide what's relevant. The engine
underneath is **RAC — Requirements as Code**, open source and built to be
air-gapped; the trust boundary is human PR review, never the agent.

## Start here

```bash
pip install rac-core   # the rac CLI + the lore MCP server
rac quickstart         # scaffold identity + your first artifact
claude mcp add lore -- rac mcp
```

Full documentation: <https://itsthelore.github.io/rac-core/>

## The repositories

One repo per concern (ADR-092):

| Repository | What it is |
|---|---|
| [rac-core](https://github.com/itsthelore/rac-core) | The engine: the `rac` CLI, validation gates, and the read-only `lore` MCP server |
| [wayfinder-router](https://github.com/itsthelore/wayfinder-router) | Deterministic prompt-complexity routing — a hard-or-easy call on every prompt, offline, no model call |
| [proofkeeper](https://github.com/itsthelore/proofkeeper) | A bring-your-own-model agent that drives your app and leaves a re-runnable Playwright test as proof for each capability |
| [rac-connectors](https://github.com/itsthelore/rac-connectors) | Export-contract consumers that feed memory, RAG, and graph backends — recall fuzzily there, verify in Lore |
| [rac-sdk](https://github.com/itsthelore/rac-sdk) | Non-Python language SDKs — thin clients over the engine's stable `--json` contracts |
| [rac-editors](https://github.com/itsthelore/rac-editors) | IDE / editor integrations, one subdir per client |
| [rac-ci](https://github.com/itsthelore/rac-ci) | The CI delivery surface — validation and gating wrappers, GitHub first |
| [rac-benchmarks](https://github.com/itsthelore/rac-benchmarks) | Evaluation suites, one subdir per benchmark |

## How the pieces fit

- **rac-core** is the system of record: it captures *what* your product should
  do and *why*, and enforces it at write time (`rac validate`, `rac gate`).
- **Proofkeeper** closes the loop: it reads those capabilities over the
  published export contract, drives your product, and proposes verification
  evidence back by pull request.
- **Wayfinder** is a sibling, not a consumer — routing is a runtime concern,
  not a knowledge one. It began as an experiment inside RAC and was split out.
- Everything else (connectors, SDKs, editors, CI) is a thin surface over the
  engine's stable contracts — the engine stays deterministic, offline, and
  in one place.

## Principles

- **Markdown-first.** Every artifact is plain Markdown with a small
  frontmatter envelope, versioned next to the code.
- **Deterministic over probabilistic.** Classification, retrieval, routing,
  and eval scoring make no model calls; the same input gives the same answer.
- **Read-only at serve time.** Agents cite recorded decisions by ID; they
  cannot mutate the store. Changes land through human-reviewed PRs.
- **Enforced in CI.** Malformed artifacts, broken links, and references to
  superseded decisions are rejected before the knowledge lands.

## Project status

Everything here is early and evolving quickly. Contributions, ideas, and
experiments welcome — start with
[rac-core](https://github.com/itsthelore/rac-core).
