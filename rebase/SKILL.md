---
name: rebase
description: Rebase the current feature branch onto the latest dev so it's up to date and can be merged cleanly.
allowed-tools: [Bash,  Read,  Edit, Glob, Grep]
---

## Golden Rules

- Under no circumstances should work be lost during a rebase.
- Destructive actions should always require explicit user confirmation (ensure they know what they are approving).
- Never merge or apply additional patches (from PRs) automatically without confirmation.

## Preparation

- Enable `git rerere` to help with conflict resolution (if not already enabled).
- Confirm we are on a feature branch (not `dev` or `main`). If not, stop and inform the user.
- Check for uncommitted changes. If there are any, stop and ask the user to commit or stash first.
- If brew is available, install the `gh` (GitHub) CLI and ask the user to authenticate (if not already done).

## Syncing

Fetch the latest `dev` branch from origin and rebase the current branch onto it, then:

- If there are conflicts, attempt to resolve them using git rerere recorded resolutions.
- Prioritize the local changes, but only if they can be resolved cleanly without losing important updates from dev.
- If conflicts cannot be auto-resolved, stop and inform the user about the conflicting files so they can resolve them manually.

## Migrations

- If the feature has migrations, ensure they are reordered correctly after the rebase to maintain a proper sequence.
- Be careful with state; never try to silently resolve conflicts without user awareness if they are not obviously resolvable.
- Once everything is clean, run the tests to ensure nothing is broken. If tests fail, attempt to fix any regressions caused by the rebase. 


## Edge cases

- In some instances, but specially when files are moved or modules/functions/constants/variables are renamed, rebasing might introduce unwated changes from into the current branch. To avoid that and to keep the current branch in the proper state (containing only the diff for our changes), you might need to simply reapply changes on top of new context. If resolution seems to difficult and can't be infered from context - you should prioritize doing it interactively with the user, so additional context and guidance can be provided to avoid data loss.

## Important

- Once the rebase is complete /review the branch to ensure there are not unintended changes (required).
- Be extremely careful when including additional code that is not related to the overall branch goal or functionality.
- Do NOT force-push automatically. Inform the user that the rebase is done and they can force-push when ready.
- If any step fails irrecoverably, stop and clearly explain what happened and how to recover (e.g., `git rebase --abort`).
- Once the user has authorized the push, use `git push --force-with-lease`. Then /loop monitor the PR CI every 5 minutes and notify the user of any blockers preventing the PR from being merged (e.g., comments, flaky tests, merge conflicts, etc).
