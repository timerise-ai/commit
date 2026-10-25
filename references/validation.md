# Validation

Running the repository's own check before committing. The check is best-effort: it is run when the repository
defines one and reported, and its absence never blocks a commit.

## Find the check

Use only checks the repository already defines. Do not install a tool or add a script to get one.

| Project | Preferred check |
|---|---|
| Node | `npm run build`, `npm run lint`, `npm run typecheck`, only the scripts that exist in `package.json`, with the package manager the lockfile names |
| Rust | `cargo check` |
| Go | `go build ./...`, then `go vet ./...` |
| Python | `ruff check`, `pytest`, only when configured in the repository |
| Other | the check the repository documents in its README, `CLAUDE.md` or `CONTRIBUTING`; otherwise none |

A repository whose `CLAUDE.md` or README names a different check, or a faster one, uses that. See
[adaptation.md](adaptation.md).

## When to skip

- **No check exists.** Commit and say that nothing was run.
- **The change cannot affect the check.** Documentation, a changelog entry or a comment-only change needs no
  build. Say it was skipped and why.
- **The check is slow and the commit is explicitly WIP.** Say it was skipped; the user can run it before the
  next real commit.

## Reading the result

- **The check runs on the working tree, not on the staged set.** If unstaged changes remain after the groups
  are formed, a pass covers code that is not in the commit. Say so in the report rather than claiming the
  commit passed.
- **A failure is reported, not hidden.** Show the failing command and the relevant lines of its output, then
  ask whether to commit anyway. A WIP commit of a failing build is legitimate when the user chooses it; say
  so in the body (`Build fails: <reason>.`).
- **Never fix code to make the check pass inside this skill** unless the user asks. The commit records the
  work as it is; fixing it is a separate task with its own commit.
