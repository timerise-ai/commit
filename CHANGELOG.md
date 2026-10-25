# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.1.0] - 2026-10-25

First release under the Timerise Skills standard: the single-file skill is
restructured into an entry point and references, audited against git's hook
behaviour and against `bumpv`, and given agent evals.

### Added

- `SKILL.md`: the flow diagram, the seam, six critical facts, six hard rules,
  the quick start and the reference directory.
- `references/staging.md`: review, untracked and already staged files, what is
  never staged, grouping, staging by path, and a file holding two changes.
- `references/validation.md`: the check per project type, when to skip it, and
  why a pass on the working tree is reported as such.
- `references/messages.md`: types and how `bumpv` reads them, scope, subject,
  body, footers, breaking changes and examples.
- `references/committing.md`: the heredoc commit, verifying what landed, hooks,
  when amending is allowed, repository states and the report.
- `references/adaptation.md`: the host's conventions and where they win, the
  contract with `bumpv`, and the non-negotiables.
- `references/provenance.md`: the audit ledger.
- `README.md`, `CHANGELOG.md`, `LICENSE`, `evals/prompts.md` and the agent eval
  workflow caller.

### Changed

- No amend after a failed hook, no amend of a pushed commit, no `--no-verify`,
  and the attribution check repeated after an amend.
- The subject limit is 72 characters including the prefix, with 50 as the aim.
- `chore(release):` is left to `bumpv`.

### Removed

- `allowed-tools` from the `SKILL.md` frontmatter, which now has `name` and
  `description` only.
