---
name: changeset
description: Propose how to split the working tree into themed commits that form a consistent narrative of work. Use when the user asks to break a branch into multiple commits or organize/ prepare pending work for a pull request.
allowed-tools: [Bash, Read, Grep]
---

# Overview

Plan and propose a sequence of commits that splits the working tree into a consistent, logical timeline of work. Each commit groups files by intent, respects dependencies, and compiles independently of later commits. Present the full list first with a short reasoning per commit, then wait for explicit user approval before committing.

## Context

1. **Survey what changed.** In parallel: `git status --short` (the file list, including untracked) and `git diff --stat HEAD` (size of each change). Gives the high-level map before reading any code.
2. **Check branch state.** `git rev-parse --abbrev-ref HEAD` and `git log <main>..HEAD --oneline`. Tells you whether the branch already has commits ahead of main (split only the working tree, or reshape the whole branch?).
3. **Learn the commit convention from the repo.** `git log --oneline -20` for the recent shape, `git log -5` for full messages. Mirror whatever the repo already does — prefix or scope tagging (if any), casing, tense, subject length, body style, and how (if at all) external IDs appear. Do not import a convention from elsewhere; if the history shows no pattern, keep messages plain and descriptive.
4. **Inspect each change.** Read diffs file by file with `git diff HEAD -- <path>`. Going one file at a time keeps each file's intent easy to isolate and avoids flooding context on large branches.
5. **Don't miss untracked files.** `git diff HEAD` skips untracked files unless they've been `git add -N`'d. Either run `git add -N <path>` first or read the new files directly — otherwise you'll silently miss their intent.

## Scoping

1. **Group by intent.** A scope is a unit of intent, not a directory or surface. Two files in different directories can share a scope; two files in the same directory can belong to different ones.
2. **Keep scopes self-contained.** A scope must compile cleanly on its own and contain nothing unrelated to its intent.
3. **Hunk-split mixed files.** When a single file's changes span multiple scopes, propose a `git add -p` split with each hunk named by the function or line range it touches, so it's clear what goes where.
4. **Order by dependency.** Identify what has to land first for later commits to build. The commit timeline should match the logical order someone would actually have done the work in.
