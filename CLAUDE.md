# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

An [Agent Skill](https://agentskills.io) package: markdown only. There is no `package.json` here and nothing
in this repository executes. It teaches an agent to turn the changes in any git working tree into clean
Conventional Commits that the release skill `bumpv` can cut a version from.

Keep the two straight: the `git` commands and checks in `references/` run in the **host** repository being
committed in, not in this one.

The skill was written by the engineer who maintains the Timerise Skills; the earlier implementation was a
single-file version of it. `references/provenance.md` is the rationale layer: what the audit changed, what
was kept deliberately and what was added here.

## Structure

- `SKILL.md`: entry point, loaded whole on every activation, so it stays between 130 and 160 lines, the
  closing index line aside. Frontmatter has `name` and a `description` that is the trigger surface; the body
  carries the flow diagram, the seam paragraph, six critical facts, six hard rules, the quick start, the
  reference directory and the closing index line.
- `README.md`: the human-facing front door, in the section order of the index's STANDARD.md.
- `references/staging.md`, `validation.md`, `messages.md`, `committing.md`: one step of the flow each.
  `adaptation.md` is the seam contract with the host repository and `bumpv`; `provenance.md` is the ledger.
- `evals/`: `prompts.md` holds what an operator types after installing, in their words; the first prompt is
  the agent eval run before every release. The skill builds no module, so the checks only confirm the fixture
  app still type-checks and builds and the notes carry the score. Every other file there is one eval run:
  measured frontmatter that is never edited, then the notes of the person who ran it. Add a prompt rather
  than rewording one that has results. The procedure is section 10 of the index's STANDARD.md.
- `.github/workflows/agent-eval.yml`: the caller of the index's reusable eval workflow, copied verbatim from
  STANDARD.md. Do not edit it, and never add a trigger on `push` or `pull_request`.

## Editing conventions

- **Identifiers are shared across files.** The eight types and their `bumpv` levels, the `BREAKING CHANGE:`
  footer, `chore(release):`, the 50 and 72 character limits, and the commands `git log -1 --format=%B`,
  `git diff --staged --stat` and `git status -sb` appear in several files. Change them in all or none.
- **The `bumpv` contract moves with `bumpv`.** The type table in `messages.md` and the integration section of
  `adaptation.md` restate `bumpv`'s decision criteria; a change to either skill's format is checked against
  the other.
- **Keep the three tables in sync** with `references/`: the reference directory and the quick start in
  `SKILL.md`, and the file table in `README.md`. Links are relative.
- **The hard rules and the non-negotiables move together**: the six hard rules in `SKILL.md`, the six
  non-negotiables in `README.md` and the list in `adaptation.md` state the same six things in the same
  order, and none is ever presented as optional.
- **The odd-looking parts stay.** Both breaking-change markers, a file with two changes committed whole,
  validation that never blocks: each has an entry in `provenance.md`. Read it before simplifying.
- **Claims about git are verified.** A statement about hooks, amend or staging is checked against git's
  documentation or a reproduction in a scratch repository, and the ledger entry says which.
- **Mark additions as additions.** Anything not in the earlier implementation and never run before goes
  under *Added* in `provenance.md`, and the ledger's entry count is updated.
- **The host renames nothing.** Its check, scopes, types and limits are read from its own files (the table
  in `adaptation.md`); every other name here is the skill's contract.
- **Plain punctuation, wrapped at 110 columns.** No em-dashes, en-dashes, arrows, middle dots or smart quotes
  in prose or tables; ranges are written "1 to 4".
- Commits follow Conventional Commits with no AI attribution trailers, and releases follow the index's
  STANDARD.md: `chore(release): X.Y.Z` containing only `CHANGELOG.md` and the README version line, tag
  `vX.Y.Z`, and a GitHub Release. Eval results are `chore(evals): ...` and never bump the version.
