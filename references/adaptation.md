# Adaptation

The seam between this skill and the repository it commits in. The host repository brings its own check, its
own scopes and sometimes its own commit rules; this skill applies them inside its non-negotiables and never
rewrites them.

## What the host must provide

- **A git working tree** with a user name and email configured. Nothing else is required: no manifest, no
  check, no commit rules.
- **Optionally, a check** (see [validation.md](validation.md)) and **documented conventions** in a
  `CLAUDE.md`, `AGENTS.md`, `CONTRIBUTING.md` or a commitlint config.

## Where the host's conventions win

| Canonical in this skill | The host may set instead | Where it is read |
|---|---|---|
| The check per project type | its own documented check command | `CLAUDE.md`, `AGENTS.md`, README, `CONTRIBUTING.md` |
| A scope chosen from the diff | its scope list, or the scopes in its recent log | `CLAUDE.md`, commitlint `scope-enum`, `git log --oneline -20` |
| The eight types of [messages.md](messages.md) | a narrower set, or extra types such as `build`, `ci`, `revert` | commitlint `type-enum`, `CONTRIBUTING.md` |
| Subject up to 72 characters | a shorter limit | commitlint `header-max-length`, `CONTRIBUTING.md` |
| No sign-off | a required `Signed-off-by:` | `CONTRIBUTING.md`, a DCO check |
| Hooks as they are | its `pre-commit`, `commit-msg` hooks, Husky or lefthook config | `.git/hooks/`, `.husky/`, `lefthook.yml` |

The Timerise Skills repositories, for example, document their commit types in `CLAUDE.md`:
`docs(readme): ...`, `feat(references): ...`, `fix(templates): ...`, and `chore(evals): ...` for eval results.

A host convention that is stricter than this skill is followed. A host convention that would break a
non-negotiable is not: report the conflict and let the user decide.

## Integration with `bumpv`

`bumpv` is the release skill that reads the history this skill writes. The contract between them:

- `bumpv` picks MAJOR from `!` after the type or scope, or a `BREAKING CHANGE:` footer; MINOR from `feat`;
  PATCH from the other types. A message format change here is a change to that contract.
- `bumpv` requires a clean working tree, so this skill's report names every file it left uncommitted.
- `bumpv` writes the `chore(release): X.Y.Z` commit, the changelog, the version and the tag. This skill
  writes none of them.

A repository that releases with another tool, such as semantic-release or release-please, reads the same
Conventional Commits markers; the contract holds unchanged.

## Order of work

1. Read the host's documented conventions, if any.
2. Review, validate, group and stage: [staging.md](staging.md), [validation.md](validation.md).
3. Write the messages in the host's vocabulary: [messages.md](messages.md).
4. Commit, verify and report: [committing.md](committing.md).

## The non-negotiables

The same six as the hard rules in `SKILL.md`, in the same order. No host convention relaxes them.

1. **Never put AI attribution in a commit**, and check `git log -1 --format=%B` after every commit.
2. **Stage by path, never with `git add .`, `-A` or `git commit -a`**; secrets, build output and large
   binaries are flagged and never staged.
3. **Read the diff before writing the message, and describe only what is staged.**
4. **Mark every breaking change twice**, with `!` and a `BREAKING CHANGE:` footer.
5. **Never bypass or misdirect a hook**: no `--no-verify`, no `--amend` after a failed commit, no amend of
   a pushed commit.
6. **Never push, tag, bump a version or edit the changelog.**
