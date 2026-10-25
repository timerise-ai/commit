# Committing

Making the commit, checking what landed, and the states of the repository that need care. Every command here
runs in the host repository.

## Commit with a heredoc

A multi-line message passed with `-m` and a quoted heredoc keeps backticks, dollar signs and quotes intact:

```bash
git commit -m "$(cat <<'EOF'
feat(reports): add CSV export

Stream rows instead of buffering so large exports don't load the
whole result set into memory.
EOF
)"
```

A one-line message can use `git commit -m "fix(auth): handle expired refresh tokens"`. Do not open an editor:
`git commit` without `-m` waits for input an agent's shell cannot give.

## Verify what landed

After every commit:

```bash
git log -1 --format=%B
git show --stat HEAD
```

- **The message is the one you wrote, with no line added.** Harness defaults and the repository's
  `prepare-commit-msg` or `commit-msg` hooks can append trailers. If an attribution line landed, remove it
  with `git commit --amend` and the corrected message, then run `git log -1 --format=%B` again, since the
  same hook runs on the amend.
- **The files are the ones you staged.** `git show --stat HEAD` should match the `git diff --staged --stat`
  you checked before committing.

## Hooks

- **A hook that fails aborts the commit.** No commit was made: fix the cause, restage if the fix touched
  files, and run the same `git commit` again. Never follow a failed commit with `--amend`, which rewrites the
  previous, unrelated commit.
- **A hook that rewrites files** (a formatter run by `pre-commit`, for example) may leave changes unstaged
  or abort the commit. Report what it changed, stage those changes with the group they belong to, and
  commit again.
- **Never pass `--no-verify`.** The hook is the repository's rule; going around it is the user's decision,
  made by the user.

## When amending is allowed

Only for the commit this skill just made, and only while it is unpushed. Check both before amending:

```bash
git log -1 --format='%h %s'
git status -sb
```

The first line must be the commit just made. `git status -sb` shows `[ahead N]` against the upstream, and
the commit must be among those N. A branch with no upstream has not been pushed. A pushed commit is never
amended by this skill; report the problem instead.

## Repository states

| State | How to see it | What to do |
|---|---|---|
| Detached HEAD | `git status` says `HEAD detached` | Warn that the commit lands on no branch, and ask before committing |
| Rebase, merge, cherry-pick or revert in progress | `git status` names the operation | Warn; committing here continues or concludes that operation. Ask before committing |
| Merge conflicts | `git status` lists `both modified` | Do not commit; conflict markers would be recorded. Report the files |
| No commits yet | `git log` fails | Commit normally; the first commit has no special form |

## Report

End with what was committed and what was not: each commit's `git log -1 --format='%h %s'`, the check that
ran and its result, the files left unstaged and why, and anything flagged as a secret or build output. Do
not push. Pushing, and releasing with `bumpv`, are the user's next steps.
