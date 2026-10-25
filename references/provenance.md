# Provenance

The engineering ledger for this skill, for the person editing it. The earlier implementation is the
single-file version of this skill that preceded 0.1.0: one `SKILL.md` with the process, the message format
and the attribution rule, used to write the history every Timerise skill is released from. The 0.1.0 audit
read it against git's documented behaviour and against `bumpv`, which reads what it writes. This ledger has
fifteen entries.

## Changed by the audit

Each entry says what changed and how the skill now holds it.

1. **A failed hook followed by an amend.** The earlier implementation said to report a failing hook "rather
   than forcing past it" and, separately, to amend when a trailer slipped in. Nothing stopped an agent from
   amending after a failed commit. A failing `pre-commit` hook aborts the commit (githooks(5), and a
   reproduction), so that amend rewrites the previous, unrelated commit. Now hard rule 5 and
   [committing.md](committing.md): recommit after a failure, never amend.
2. **Amending without checking that the commit is the one just made and is unpushed.** Now
   [committing.md](committing.md) checks both with `git log -1` and `git status -sb` before any amend.
3. **The re-check after an amend.** `prepare-commit-msg` runs on `git commit --amend` (githooks(5)), and so
   does `commit-msg` (a reproduction in a scratch repository), so a hook that appended the trailer appends it
   again. Now the check is repeated after the amend.
4. **`--no-verify` was not named.** Now hard rule 5 names it.
5. **Interactive staging.** The earlier implementation asked for focused commits but gave no way to split a
   file in a shell without input; `git add -p` waits for an answer. Now [staging.md](staging.md) commits
   such a file whole and says so in the body, and critical fact 6 states why.
6. **The check runs on the working tree.** The earlier implementation ran it before staging and did not say
   that a pass covers unstaged code too. Now critical fact 4 and [validation.md](validation.md) say how to
   report it.
7. **"Never exceed ~72".** An approximate limit cannot be checked. Now 72 characters including the prefix,
   with 50 as the aim.
8. **`chore(release):` was not reserved.** Now hard rule 6 and [messages.md](messages.md) leave it to
   `bumpv`.
9. **A secret already staged by the user.** The earlier implementation only scanned before staging. Now
   [staging.md](staging.md) unstages it with `git restore --staged` and says so.
10. **The attribution rule was stated in two places in different words.** Now it is hard rule 1 in
    `SKILL.md`, with the full list of forbidden lines in [messages.md](messages.md), and a human
    co-author the user names is explicitly allowed.
11. **`allowed-tools` in the frontmatter.** The index standard gives every skill two frontmatter fields, so
    the field is removed; the skill's commands are all `git` and the repository's own check.

## Kept deliberately

12. **Both breaking-change markers.** The Conventional Commits specification accepts either alone. Both stay
    because `git log --oneline` shows only the `!`, and only the footer carries the migration path into the
    changelog.
13. **Validation never blocks.** A WIP or documentation commit with no check, or with a failing build the
    user chooses to record, is legitimate; refusing it would push users to commit by hand and skip every
    other rule.

## Added

Designed in this skill and never run before 0.1.0:

14. **Reading the recent log and the host's documented conventions** for scopes, types and checks, with the
    rule that a stricter host convention is followed and one that breaks a non-negotiable is reported
    ([adaptation.md](adaptation.md)); the repository-state table and the end-of-run report in
    [committing.md](committing.md); and the agent evals in `evals/`.
15. **A file only the check rewrote is left unstaged and reported** ([validation.md](validation.md)), found by
    the 0.1.0 agent evals: `next build` rewrote `tsconfig.json` during the check, and one of three agents
    staged it inside the feature commit, whose message did not mention it. The other two reverted it. The
    rewrite is reproduced in every eval log of that release; leaving it in the tree is neither run nor
    verified before 0.1.1.
