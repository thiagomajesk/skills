# Mutators

What each mutation operator does and when it adds signal. The ast-grep rules themselves live in `mutators/<lang>.yml`.

To add an operator: drop a rule into the matching language file (or create a new one), then describe it here using the same id. A good operator changes observable behaviour on at least some input, targets idioms developers actually write, and stays compilable after the fix.

## Operators

### `comparison-flip-lt`

`<` → `<=`. Tests off-by-one at a lower bound or loop start. Example: `for (i = 0; i < n; i++)` becomes `for (i = 0; i <= n; i++)` — one extra iteration.

### `comparison-flip-gt`

`>` → `>=`. Tests off-by-one at an upper bound. Example: `if (count > limit) throw` becomes `if (count >= limit) throw` — fires on equality too.

### `equality-flip`

`===` → `!==` (TS/JS) or `==` → `!=` (Elixir). Catches tests that don't assert on the equality outcome — the branch fires for every other value.

### `boolean-and-to-or`

`&&` → `||`. Catches tests that don't exercise both operands of a conjunction. Skip when `$A` is a guard that throws (e.g. `assert(x) && useX()`) — the mutation may be killed by the assertion, not the test.

### `and-to-or` *(Elixir, strict booleans)*

`and` → `or`. Same intent as `boolean-and-to-or`, but for Elixir's strict-boolean operator. Run alongside `andand-to-oror`; they target different call sites.

### `andand-to-oror` *(Elixir, truthy)*

`&&` → `||`. Elixir's truthy-operator conjunction flip.

### `arith-plus-to-minus`

`+` → `-`. Catches tests that assert on a sum's value. In TS/JS, `+` matches both arithmetic and string concatenation — verify the test signal is real after applying.

### `boolean-true-to-false`

`true` → `false`. Catches assertions on hard-coded `true` returns and flags. The literal also matches config-object values and defaults — skip candidates that aren't on a test path.

### `boolean-false-to-true`

`false` → `true`. Same shape as above, in the opposite direction. Often catches missing assertions on early-return / null-fallback cases (e.g. `return x ?? false`).

### `optional-chain-drop` *(TS/JS)*

`$A?.$B` → `$A.$B`. Tests should cover the null/undefined branch of an optional member access — without the chain, the access throws instead of returning undefined.

### `await-drop` *(TS/JS)*

`await $E` → `$E`. An async test should fail when the call stops waiting for the underlying promise; the unawaited value is a `Promise`, not the resolved value.

### `array-filter-keep-all` *(TS/JS)*

`$ARR.filter($PRED)` → `$ARR`. A test asserting on filtered contents should fail when the filter becomes a no-op and inactive entries pass through.

### `ok-to-error` *(Elixir)*

`{:ok, $X}` → `{:error, $X}`. Tests that pattern-match on `{:ok, _}` will fail loudly; tests that just discard the wrapper won't notice. Both signals are informative.

### `pipe-step-removal` *(Elixir)*

`$A |> $F($$$ARGS)` → `$A`. Drops one stage of a pipeline. The most aggressive operator here — easily produces uncompilable mutants in the middle of long pipes. Reserve for steps that are observably side-effecting (transformation, validation, formatting).
