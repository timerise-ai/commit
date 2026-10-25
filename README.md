# commit

[![Agent Skills](https://img.shields.io/badge/Agent_Skills-open_format-059669)](https://agentskills.io)
[![skills.sh](https://img.shields.io/badge/skills.sh-npx_skills_add-059669)](https://www.skills.sh)
[![Claude Code](https://img.shields.io/badge/Claude_Code-compatible-059669)](https://docs.claude.com/en/docs/claude-code/skills)
[![Codex CLI](https://img.shields.io/badge/Codex_CLI-compatible-059669)](https://developers.openai.com/codex/skills)
[![Gemini CLI](https://img.shields.io/badge/Gemini_CLI-compatible-059669)](https://github.com/google-gemini/gemini-cli/blob/main/docs/cli/skills.md)

An [Agent Skill](https://agentskills.io) that teaches an agent to turn the changes in any git working tree
into clean Conventional Commits: read the diff, run the repository's own check, stage by path, split unrelated
changes into separate commits, write the message with the markers the release tooling reads, and verify what
landed. It works in any repository and any stack, the Next.js apps of the other Timerise Skills included.

**The history is the release input.** The release skill `bumpv` decides the next version from the commits
alone, so a commit that hides a breaking change, sweeps in a secret or carries a tool's trailer ships that
mistake in the next release. Stage by path, write the markers, and check what landed.

This skill was written by the engineer who maintains the Timerise Skills and releases every one of them from
the history it writes. The earlier implementation was a single-file version of the skill. What it carries
holds by the commands it runs: every file staged by explicit path and listed with `git diff --staged --stat`
before the commit; every breaking change marked with both `!` and a `BREAKING CHANGE:` footer; every commit's
message read back with `git log -1 --format=%B`, so no attribution trailer stays; and no amend after a failed
hook or on a pushed commit. [`references/provenance.md`](references/provenance.md) has the record.

## Install

One command, via the [skills.sh](https://www.skills.sh) CLI, which installs the skill into every
skills-compatible agent it detects, including Claude Code, Codex CLI and Gemini CLI:

```bash
npx skills add timerise-ai/commit
```

Name the agents instead with `-a`, for example `npx skills add timerise-ai/commit -a claude-code -a codex`.

### Manual install

Nothing here is Claude-specific: the skill is a plain [Agent Skills](https://agentskills.io) folder,
`SKILL.md` plus markdown references with no file that calls a model, so cloning it into an agent's skills
directory is all an install is. For Claude Code:

```bash
git clone https://github.com/timerise-ai/commit.git ~/.claude/skills/commit
```

To scope it to a single project instead, clone it into that project's `.claude/skills/` directory. For another
agent, clone into that agent's skills directory, or symlink the Claude Code copy so one `git pull` updates
every agent:

```bash
mkdir -p ~/.agents/skills
ln -s ~/.claude/skills/commit ~/.agents/skills/commit
```

Update the skill with `git pull` in its directory. The current release is **0.1.1**. See
[CHANGELOG.md](CHANGELOG.md). The [skills index](https://github.com/timerise-ai/skills) lists the other
Timerise Skills and how to install them all at once.

## Activation

The skill activates automatically when the user asks to commit, save progress, record WIP, split the working
tree into commits, or wrap up a finished task. Invoke it explicitly with `/commit` in Claude Code, `$commit`
in Codex CLI, or from `/skills` in Gemini CLI.

Each host matches a task against the description its own way, so invoke the skill explicitly on a first run
rather than assuming it fired. Only `SKILL.md` is read up front; the `references/` files load on demand.

## What's inside

| File | Contents |
|---|---|
| `SKILL.md` | Entry point: the flow diagram, the seam, critical facts, hard rules, quick start and reference directory |
| `references/staging.md` | Reviewing the tree, untracked files, what is never staged, grouping, staging by path, a file with two changes |
| `references/validation.md` | Finding the repository's check per project type, when to skip it, and reading its result |
| `references/messages.md` | Types and how `bumpv` reads them, scope, subject, body, footers, breaking changes, examples |
| `references/committing.md` | The heredoc commit, verifying what landed, hooks, when amending is allowed, repository states, the report |
| `references/adaptation.md` | The seam contract: the host's conventions and where they win, the contract with `bumpv`, the order of work, the non-negotiables |
| `references/provenance.md` | The engineering ledger: what the audit changed, what was kept deliberately, and what was added here |
| `README.md` | This file |
| `CHANGELOG.md` | One section per release, newest first |
| `CLAUDE.md` | The editing conventions, for an agent editing this repository |
| `LICENSE` | MIT |
| `evals/` | The prompts an operator types after installing (`prompts.md`) and one file per agent eval: the skill installed into an empty Next.js app, one prompt that asks for a change and its commit, no help, then type-checked and built |
| `.github/workflows/agent-eval.yml` | The caller of the index's reusable eval workflow, run on every published release and on a maintainer's dispatch |

The skill builds no module, so its evals score the agent's fidelity to the hard rules, not the app: the checks
only confirm the app still type-checks and builds, and the notes on each run carry the score. The seam with
the host repository is its own check command, its scope vocabulary and its documented commit conventions,
read and never rewritten. A stricter host convention is followed; one that would break a non-negotiable is
reported to the user.

## The six non-negotiables

These are never optional. Each is stated as a hard rule in `SKILL.md`, in the same order:

1. **Never put AI attribution in a commit.** The history is read by people and by release tooling, and a
   tool's trailer belongs to neither; `git log -1 --format=%B` after every commit verifies it, and an amend
   removes one that a harness or hook added.
2. **Stage by path, never with `git add .`, `-A` or `git commit -a`.** Each sweeps in whatever is lying in
   the tree; secrets, build output and large binaries are flagged and never staged, and
   `git diff --staged --stat` lists the commit before it is made.
3. **Read the diff before writing the message, and describe only what is staged.** The message is what the
   release notes are written from, so it is checked against `git diff --staged`, not against the task.
4. **Mark every breaking change twice.** `bumpv` picks a MAJOR from the markers alone; the `!` shows in
   `git log --oneline` and the `BREAKING CHANGE:` footer carries the migration path into the changelog.
5. **Never bypass or misdirect a hook.** A hook is the repository's rule, so no `--no-verify`; a failed hook
   leaves no commit, so an amend after it rewrites the previous commit; and a pushed commit is never amended.
6. **Never push, tag, bump a version or edit the changelog.** The release belongs to `bumpv`, which writes
   `chore(release):`; pushing is the user's call.

Everything else is the host repository's: its check, its scopes, its commit rules, its hooks.

## Not this

| Not this | Use instead |
|---|---|
| Bumping the version, writing the changelog, tagging a release | `bumpv` |
| Pushing, or opening and describing a pull request | the user, after this skill reports |
| Rewriting published history: rebase, squash, force push | a deliberate, separately confirmed step |
| Reviewing the change for bugs before it is committed | a code review skill |
| Hardening a skill from its agent evals | [`skill-eval-loop`](https://github.com/timerise-ai/skill-eval-loop) |

## Contributing

Issues and pull requests are welcome here. Pure markdown, with no build step and nothing that executes; the
commands in `references/` run in the repository being committed in, not this one. Claims in this skill are
meant to be verifiable: if you change a factual claim about git, a hook or the commit format, say how you
verified it, whether against git's own documentation (githooks(5), `git help commit`), the Conventional
Commits specification, `bumpv`'s decision criteria, or a reproduction in a scratch repository.

Adding, removing or renaming a file in `references/` means updating the quick start and the reference
directory table in `SKILL.md`, the file table above, and any relative cross-links.
`references/provenance.md` is the ledger that must stay truthful; add an entry for anything you change.
Commits follow Conventional Commits and releases follow
[STANDARD.md](https://github.com/timerise-ai/skills/blob/main/STANDARD.md) in the index; `CLAUDE.md`
carries the full editing conventions.

## Part of the Timerise Skills

This is one of the [Timerise Skills](https://github.com/timerise-ai/skills): modules for **Next.js App
Router** apps written by our own senior engineers from the modules they have shipped, not synthetic, each
published as its own repository and indexed there. They share one layout, so an agent that has read one knows
how to read the next: a `SKILL.md` entry point, `references/` loaded on demand, and a seam contract carrying
the module's non-negotiables. This one is for any repository: it writes the history the skills are released
from.

## Author

Built and maintained by [Timerise](https://timerise.ai).

## License

MIT. See [LICENSE](LICENSE).
