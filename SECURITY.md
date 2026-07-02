# Security Policy

This is the default security policy for all repositories in the
**itsthelore** organisation. A repository with its own `SECURITY.md`
(for example [rac-core](https://github.com/itsthelore/rac-core/blob/main/SECURITY.md)
or [wayfinder-router](https://github.com/itsthelore/wayfinder-router/blob/main/SECURITY.md))
adds repo-specific scope and threat-model detail on top of this policy —
the reporting route below applies everywhere.

## Reporting a vulnerability

Please report security issues **privately** — don't open a public issue or
pull request.

Use GitHub's private vulnerability reporting on the affected repository:
*Security → Advisories → Report a vulnerability*. We'll acknowledge within a
few days and keep you posted as we work on a fix.

If private reporting is unavailable on a repository, email
<tom.ballard08@gmail.com> instead.

## Supported versions

Releases follow CalVer and fixes ship on the latest release. Please reproduce
against the most recent version before reporting.

## Design posture

A few properties are load-bearing across the organisation, and reports that
undermine them are especially welcome:

- **Engines are deterministic and offline.** The RAC engine and the Wayfinder
  scored decision path make no model calls and no network calls in their core
  paths; the only egress anywhere is a consent-gated, content-free usage ping
  that is off by default.
- **Serving surfaces are read-only.** The `lore` MCP server never writes,
  never executes artifact content, and re-reads from disk on every call.
- **The trust boundary is human PR review.** Artifact content is untrusted
  input until a human has reviewed and merged it; nothing machine-ingested is
  treated as trusted automatically.
- **Keys stay in the environment.** Provider credentials are read at request
  time and are never written to config, logs, or disk.
