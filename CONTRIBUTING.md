# Contributing to itsthelore projects

Thanks for your interest. This is the organisation-wide baseline; a repository
with its own `CONTRIBUTING.md` (for example
[rac-core](https://github.com/itsthelore/rac-core/blob/main/CONTRIBUTING.md),
[wayfinder-router](https://github.com/itsthelore/wayfinder-router/blob/main/CONTRIBUTING.md),
or [proofkeeper](https://github.com/itsthelore/proofkeeper/blob/main/CONTRIBUTING.md))
adds project-specific setup and verification steps on top of it.

## Ground rules

- **Be kind.** The [Code of Conduct](CODE_OF_CONDUCT.md) applies in every
  community space.
- **All code is Apache 2.0.** By contributing you agree your contribution is
  licensed under the repository's [Apache License 2.0](LICENSE).
- **Small, single-concern pull requests.** One area per PR; keep refactors
  separate from behaviour changes.

## Commits and pull requests

- Use conventional, single-scope commit titles:
  `type(area): imperative summary` with types `feat`, `fix`, `test`, `docs`,
  `refactor`, `chore`.
- Write a descriptive body when the summary alone can't carry the why.
- **No AI attribution** in commits, PR titles or bodies, or comments — no
  generated-by footers, no tool `Co-Authored-By` trailers, no session links.
  Commits carry the contributor's own identity.

## Before you open a PR

- Run the repository's test suite and linters; each repo's `CONTRIBUTING.md`
  or `README.md` lists the exact commands.
- In repositories that carry a `rac/` corpus, `rac validate rac/` and
  `rac relationships rac/ --validate` must pass — CI gates on them.
- Behaviour changes generally deserve a changelog entry, and design decisions
  belong in the repository's decision record (ADRs / the `rac/` corpus), not
  only in the PR description.

## Where to start

Not sure where a change belongs? The organisation keeps one repository per
concern — the [org README](profile/README.md) maps them. When in doubt, open
an issue on [rac-core](https://github.com/itsthelore/rac-core/issues) and ask.
