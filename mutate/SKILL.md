---
name: mutate
description: Run mutation testing on a small, scoped slice of code to verify the tests would actually catch a regression. Use when the user asks to check test quality, find weak spots in a test suite, or verify that tests would notice a behaviour change.
allowed-tools: [Bash, Read, Edit, Write, Grep]
---

# Overview

This skill runs mutation testing on the diff of the current feature branch and surfaces tests that execute changed code without asserting on its behaviour. The final output is a list of *survivors* — mutations the suite did not catch — that the user can address by tightening tests. Each step is a self-documenting script (`scripts/<name>`); read the script header for flags and examples, and invoke the script by name. Do not inline its logic.

## Prerequisites

The user needs to have `ast-grep` installed on their system. Pick the simplest approach based on the operating system and the available tooling. The preferred order is brew -> npm -> cargo:

```bash
# macOS/linux
brew install ast-grep

# npm
npm install -g @ast-grep/cli

# cargo
cargo install ast-grep
```

Verify installation:

```bash
ast-grep --version
```

Then, install the skill globally (if not already available):

```
npx skills add ast-grep/agent-skill
```

## Mutation Testing

A mutation is a small behaviour-changing edit to the source (flipping `<` to `<=`, dropping an `await`, etc.). Apply one, re-run the suite: if a test fails, the mutation is *killed*; if the suite stays green, it *survived*. Survival means the line ran but no assertion depended on what it did — a coverage report would call that line tested, mutation testing reveals it isn't. A survivor is the actionable output; killed mutations confirm the suite is doing its job.

# Workflow

Pick an adapter (`scripts/adapter/<language>/<framework>`), the test root, the scope (narrowest path covering the diff), and the parallelism N before starting. Then run each step in order.

1. **Precheck.** Refuse to proceed if the working tree is dirty, HEAD is detached, or the branch is `main` / `master` / `dev` — mutation testing rewrites files, so only committed work on a feature branch is safe. Script: `scripts/precheck`.

2. **Target.** Intersect the branch diff with the suite's coverage and emit `<file>:<line>` pairs that are both changed *and* exercised. Lines outside the intersection would auto-survive (no test runs them) or be off-topic (not on this branch). Script: `scripts/target`.

3. **Sandbox setup.** Create N detached-HEAD worktrees under `/tmp/mutation-<i>` so each mutation applies atomically and reverts via `git checkout HEAD` between runs. The main checkout is never touched; pass `--link` for each gitignored build-artefact directory (e.g. `node_modules`, `_build`, `deps`) so tests run without reinstalls. Script: `scripts/sandbox setup N`.

4. **Preflight.** Confirm ast-grep can parse every target file and that the adapter reports a green baseline inside the sandbox. Mutation results are meaningless otherwise. Script: `scripts/preflight`.

5. **Enumerate.** Run `ast-grep scan` with the rule catalogue (`references/mutators/`) over the target files and emit a JSONL stream of candidate mutations — one match per line, carrying `ruleId`, `replacement`, and the byte offsets to apply. Script: `scripts/enumerate`.

6. **Curate.** Manual step: read the candidates and drop **true equivalent mutants only** — mutations indistinguishable from the original on every reachable input (e.g. `i++` vs `i = i + 1`, an optional chain after an early-return guard that already proves the receiver is non-null). A mutation that *could* differ on some input but tests don't probe is a survivor, not an equivalent — keep it. If curation takes more than 60 seconds of reasoning, the scope is too wide; narrow it and retry.

7. **Apply.** Spread the candidates across the N sandboxes, run the test command against each mutation, classify outcomes as killed / survived / compile-error, and emit one JSON record per candidate. Each mutation reverts before the next is applied. Script: `scripts/apply`.

# Reporting

`scripts/report` reads the results JSONL and emits one line per survivor: `<file>:<line>  <ruleId>  <text> -> <replacement>`. Survivors are the entire signal the skill produces. Present them, ask the user which to address, stop. Do not infer "real gap" vs "edge case", do not narrate what the mutation would do, do not group survivors by theme. If there are zero survivors, say so and stop.

# Cleanup

`scripts/sandbox teardown N` removes the sandboxes and the `/tmp/mutate-*` and `/tmp/mutation-*` scratch files.
