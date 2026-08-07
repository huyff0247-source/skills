---
name: git-workflow
description: Git best practices: atomic commits, clean history, safe recovery. Use when committing, branching, rebasing, or recovering from git mistakes.
trigger: \bgit commit\b ;; \bgit rebase\b ;; \bgit merge\b ;; \bgit branch\b ;; \bgit push\b ;; \bgit pull\b ;; \bpull request\b ;; \bPR\b ;; \bgit history\b ;; \bgit workflow\b ;; \bcommit message\b ;; \bviết commit\b ;; \bgit.*commit\b ;; \bgit.*rebase\b ;; \bgit.*merge\b
---

# Git Workflow (Compact)

## Commit Rules
- **Atomic**: one logical change per commit. Use `git add -p` to split.
- **Message**: imperative subject <72 chars, body explains WHY not what.
- **Every commit must build and pass tests** — or bisect breaks.

## Branch Hygiene
- `git rebase -i` to squash fixups before review.
- Rebase private branches, **merge shared ones**.
- `git push --force-with-lease`, never `--force` on shared branches.
- `git commit --fixup <sha>` + `git rebase -i --autosquash` for clean fixups.

## Recovery
- `git reflog` — almost nothing is truly lost.
- `git bisect run ./test.sh` — automated regression hunt.
- Enable `rerere` — remembers conflict resolutions.

## Never
- Commit generated files, secrets, or large binaries.
- Rewrite history others have pulled.
- Leave `--force` without `--with-lease`.