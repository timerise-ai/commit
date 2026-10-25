# Staging

What changed, what belongs together and what goes into each commit. This file assumes a git working tree and
a shell; it names no stack.

## Review first

Run these before anything is staged or written:

```bash
git status
git diff
git diff --staged
git log --oneline -20
```

- **`git status` is the only one that lists untracked files.** A new file never appears in `git diff`, so
  read the untracked list on its own and decide for each file.
- **`git diff --staged` shows what the user already staged.** Respect it: if the staged set is one coherent
  change, commit it as it stands and handle the rest after. If it mixes unrelated work, say so before
  restaging.
- **`git log --oneline -20` gives the repository's scopes and tone.** Reuse a scope that already exists
  rather than inventing a synonym for it. See [messages.md](messages.md).
- **Read the diff, not the file names.** The message describes the change, and a file name rarely says what
  changed in it.

## Never staged

These are flagged to the user and left out, whatever the request says. The user may stage them by hand.

| Kind | Examples |
|---|---|
| Secrets and credentials | `.env`, `.env.local`, `*.pem`, `*.key`, `id_rsa`, `credentials.json`, a file holding a token or API key |
| Build output and dependencies | `node_modules/`, `dist/`, `build/`, `.next/`, `target/`, `__pycache__/`, `*.tsbuildinfo` |
| Large binaries | archives, disk images, video, database dumps |
| Local and editor state | `.DS_Store`, `.idea/`, a local database file, a log file |

A file outside these lists that holds something that looks like a secret, such as a line `API_KEY=...` in a
notes file, is flagged the same way. If such a file is already staged, unstage it with
`git restore --staged <path>` and say so. Do not edit `.gitignore` unless the user asks; suggest the line
instead.

## Group, then stage by path

- **One commit per unrelated change.** A bug fix and a new feature in the same tree are two commits, even
  when the user asked to "commit everything". Order them so each commit builds on the one before it.
- **Stage each group by explicit path:** `git add <path> <path>`. Never `git add .`, `git add -A` or
  `git commit -a`: each sweeps in whatever is lying in the tree.
- **Deleted and renamed files are paths too.** `git add <old-path> <new-path>` records a rename; `git rm`
  is not needed for a file already deleted from disk, `git add <path>` stages the deletion.
- **A single file holding two unrelated changes is committed whole.** Interactive staging (`git add -p`,
  `git add -i`) waits for input an agent's shell cannot give. Put the file in the commit it mostly belongs
  to and say in the body that it also carries the other change, or ask the user to split it by hand.
- **Check before committing:** `git diff --staged --stat` lists exactly what the commit will hold. Nothing on
  that list should surprise you.

## Nothing to commit

If `git status` shows a clean tree, or only files that are never staged, say so and stop. Do not create an
empty commit.
