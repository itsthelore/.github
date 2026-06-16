# Lore

<!-- mcp-name: io.github.tcballard/lore -->

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/tcballard/requirements-as-code/main/rac/assets/images/lore-header-dark.png">
  <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/tcballard/requirements-as-code/main/rac/assets/images/lore-header-light.png">
  <img alt="Lore — agents that know why. Deterministic. Read-only. No RAG, no guessing." src="https://raw.githubusercontent.com/tcballard/requirements-as-code/main/rac/assets/images/lore-header-light.png">
</picture>

[![CI](https://github.com/tcballard/requirements-as-code/actions/workflows/ci.yml/badge.svg)](https://github.com/tcballard/requirements-as-code/actions/workflows/ci.yml) [![PyPI](https://img.shields.io/pypi/v/requirements-as-code)](https://pypi.org/project/requirements-as-code/) [![Python](https://img.shields.io/pypi/pyversions/requirements-as-code)](https://pypi.org/project/requirements-as-code/) [![License: MIT](https://img.shields.io/pypi/l/requirements-as-code)](https://github.com/tcballard/requirements-as-code/blob/main/LICENSE)

> **Your coding agent keeps undoing decisions your team already made. Lore stops it.**

Agents that know *why*. **Deterministic. Read-only. No RAG, no guessing.**

Lore keeps your team's recorded knowledge — requirements, decisions, designs, roadmaps, and prompts — as typed Markdown in your repo, validates it in CI, and serves it **read-only** to Claude Code, Cursor, and Claude Desktop over MCP. When the agent is about to touch something a decision already covers, it retrieves that decision and cites it by ID instead of relitigating it.

Lore is built on **RAC — Requirements as Code**, the open-source engine underneath. The package, CLI, and MCP server ship under the `rac` name; the brand is Lore.

## The problem

Coding agents have no memory of decisions they did not personally make. Your team wrote an ADR ruling out hard deletes; six weeks later the agent implements `UserRepository.delete()` as a hard delete because nothing put the decision in front of it. The cost of an agent isn't the code it writes — it's the reviewed decisions it quietly reverses, and the review cycles you burn catching them.

`CLAUDE.md` and pasted context don't solve this at team scale: they're unstructured, unenforced, and drift the moment a decision changes. Lore makes the *why* a typed, validated artifact that lives next to the code and is served to the agent on demand.

## How it works

```
record          →   validate (CI)        →   serve (MCP)            →   agent cites
typed Markdown      reject malformed,         four read-only tools       retrieves the
ADRs/requirements   broken, or superseded     over MCP                   decision and
in your repo        artifacts at write time                              cites it by ID
```

No model in the core. No embeddings. No retrieval roulette. An artifact is found by ID and keyword, returned verbatim, and the agent cannot edit it.

## Install

```bash
pip install requirements-as-code
```

Requires Python 3.11+. `uv tool install requirements-as-code` also works.

## Connect your agent

**Claude Code** (from your repo root):

```bash
claude mcp add lore -- rac mcp --root /path/to/your/repo
```

**Claude Desktop / Cursor** — add to your client's `mcpServers` config (`.cursor/mcp.json` for Cursor, `claude_desktop_config.json` for Claude Desktop):

```json
{
  "mcpServers": {
    "lore": {
      "command": "rac",
      "args": ["mcp", "--root", "/path/to/your/repo"]
    }
  }
}
```

`--root` is the directory holding your artifacts — the same path you'd pass to `rac validate`. It doesn't have to be the top of a Git repo.

## The four tools

The MCP surface is deliberately small and entirely read-only. The tool descriptions carry the trigger language, so well-tuned agents call them without being told to.

| Tool | When the agent calls it |
|---|---|
| `get_summary` | Once at session start — counts artifacts, flags health issues |
| `search_artifacts` | Before designing or implementing anything a recorded decision might cover |
| `get_artifact` | When an artifact ID appears, or before changing anything a decision covers |
| `get_related` | After retrieving an artifact — finds what else the change could affect |

There is no fifth tool, and none of them write.

## Try it in 60 seconds

Lore ships with an example corpus — one requirement, decision, design, and roadmap for a fictional user-management service — so you can see the loop before touching your own repo:

```bash
rac mcp --root examples/guide
```

Then ask your connected agent:

> What decisions has this repository recorded about data deletion?

The agent calls `search_artifacts` for `"soft-delete"`, retrieves `ADR-001: Soft-Delete User Records` via `get_artifact`, and cites the decision ID in its answer — rather than guessing or proposing a hard delete.

## Author and enforce artifacts

```bash
rac quickstart                                  # set up identity + scaffold your first artifact
rac validate rac/                               # check every artifact in a directory
rac inspect requirement.md                      # see its type and completeness
rac review rac/                                 # full repository review, worst problems first
rac relationships --validate rac/               # check the cross-artifact graph
rac export rac/ --html --out lore-export.html   # the Portal, one self-contained file
```

Run `rac validate` (and `rac relationships --validate`) in CI to reject malformed artifacts, broken or ambiguous links, references to superseded decisions, and relationship edges a type doesn't support — deterministically, before the knowledge lands.

### Importing existing decisions

Already have decisions in Confluence, Notion, or loose Markdown? The `rac-import` agent skill turns **one** existing document into **one** valid RAC artifact, with a human-review step before anything is written:

```bash
rac skill install rac-import   # installs into .claude/skills/ (Claude Code / Cursor auto-discover)
```

Then ask your agent, in plain language: *"import this decision doc into RAC."* It reads the real schema, drafts the artifact from **only** what your document says, shows you the proposed type, title, and relationships to confirm or correct, and closes on `rac validate` so it never leaves an invalid artifact behind. For bulk or multi-format conversion, use `rac-ingest`.

## What Lore is not

- **Not RAG.** No embeddings, no vector store, no nearest-neighbour guessing. Artifacts are found by ID and keyword and returned verbatim.
- **Not a writer.** The MCP server is read-only. The agent can read your decisions; it cannot rewrite them.
- **Not an AI product with a model in the loop.** The validation and serving core is deterministic. The same artifact, queried the same way, returns the same bytes every time. That determinism is the point — it's what makes the grounding trustworthy.

## How it relates to OKF

Google's Open Knowledge Format (OKF) standardises the *carrier* — a Git tree of Markdown with YAML front matter — and is deliberately permissive: an OKF consumer must not reject a bundle for missing fields, unknown types, or broken links. RAC writes that same carrier and adds what OKF leaves to the consumer: **write-time enforcement** in CI. OKF is read-optimised interchange; RAC is write-time enforcement. `rac export --okf` turns any RAC repo into a conformant OKF bundle, so the two compose rather than compete.

## Who it's for

- **Teams running coding agents heavily** (Claude Code, Cursor) who are tired of the agent ignoring decisions the team already made.
- **Teams who already write ADRs** and want those decisions to actually shape what the agent does.
- **Anyone who wants the *why* behind their software versioned alongside the code.**

## Documentation

**Full documentation: <https://tcballard.github.io/requirements-as-code/>**

- [Quickstart](https://tcballard.github.io/requirements-as-code/quickstart/) — install and author your first artifact
- [MCP server](https://tcballard.github.io/requirements-as-code/mcp/) — tools, client configuration, examples
- [CLI reference](https://tcballard.github.io/requirements-as-code/cli/) — every command, flag, and exit code

## Project status

Lore is early and evolving quickly. The MCP server ships today. Contributions, ideas, and experiments are welcome — see [CONTRIBUTING.md](https://github.com/tcballard/requirements-as-code/blob/main/CONTRIBUTING.md).

## License

MIT
