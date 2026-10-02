---
name: scoped-tests
description: Run only the tests affected by current changes instead of the full suite, and report a terse summary. Use whenever you are about to run tests for any reason (after an edit, before a commit or PR, when asked to "run the tests"), or when the user invokes /scoped-tests. Full suite only on explicit request, active debugging, or pre-merge.
---

# Scoped Tests

The default is the smallest test run that covers what changed. A full suite is something you choose deliberately and say why. Re-running a 20-minute suite after every small edit, across several concurrent PRs, slows everyone down and can still miss things. Running tests is cheap in tokens. Reading their output is not.

## Invocation

Takes an optional argument:

- *(none)*: scoped run against the current changes.
- `full`: whole suite. The user explicitly asked for it.
- `pre-merge`: whole suite, once, as the last gate before merging.

## 1. Check the run is still worth doing

Do this before anything else. Don't assume.

- **Is the target still live?** If the work belongs to a PR, check its state (`gh pr view --json state` or equivalent). If it's already merged or closed, don't run tests for it. Say so instead.
- **Is an equivalent run already going?** Check for test processes you or another session started earlier. Don't start a duplicate. If a run is going for an outdated commit, say so and offer to kill it. Don't kill it silently. It may belong to a teammate.
- **Shared state.** If the suite writes to a shared resource (a database, a fixed port, a fixed temp directory), never run two suites against it at once. They will overwrite each other and produce failures that aren't real.

## 2. Find what changed

- Base: the merge-base with the target branch (usually `main`), unless the user names another base.
- Changed files = committed diff since base + staged + unstaged + untracked (exclude ignored files).
- If nothing changed, say so and stop. Don't run anything "just to be safe."

## 3. Map changes to tests

Use the first of these that applies:

1. **Project test map.** If the repo has `.claude/test-map.md` (see `test-map-template.md`) or a Testing section in `CLAUDE.md`, follow it. It overrides everything below.
2. **The framework's own change detection.** Examples: `jest --findRelatedTests` / `--changedSince`, `vitest related`, `pytest --testmon`, `nx affected`, `turbo --filter=...[base]`, `go test` scoped to changed packages, `dotnet test` scoped to affected test projects or `--filter`. The framework's dependency graph is more accurate than your guess.
3. **Heuristics, in order:** the test file that matches the changed file by naming convention, tests in the same module or package, then tests that import the changed module (grep for it).

**Escalate instead of guessing** when a change touches something with no clear test boundary: shared fixtures or test helpers, test or build config, dependency manifests or lockfiles, DB migrations or schema, a cross-cutting module that many things import. Run the scoped set you can identify, then state that the change probably needs the full suite and ask. Don't silently widen to full. Don't silently narrow either.

Changes that are only docs or comments need no tests. Say that and stop.

## 4. Run it

- Use the project's normal test command with the narrowest selector. Ask the reporter for summary-level output (quiet or dot reporter, `-q`, `--silent`, `--verbosity minimal`) and send the full log to a file in a temp directory. Never stream it into the conversation.
- **Long runs** (roughly over 5 minutes, or a full suite): before starting, offer to give the user the exact command to run in their own terminal. A run there costs no tokens. If you run it yourself, run it in the background and wait for completion. Don't poll it or narrate progress.
- Run each test set once per change. Re-run only after something changed that could affect the result, or to check whether a failure is flaky. Say which reason applies.

## 5. Report

Keep it short:

- What ran: scoped (which selectors, and why those) or full (and which trigger justified it).
- Result: passed / failed / skipped counts, duration.
- For each failure: test name, file:line, and the few lines of assertion or error that explain it. Point to the log file for the rest. Don't paste it.
- What was deliberately *not* run, if a reader might assume it was ("API integration suite not run; no changes under `api/`").

## When the full suite is justified

Only these:

- The user explicitly asked (`full`).
- Pre-merge, run once after the last change. If several PRs are about to land together, run it once on the combined result, not once per PR.
- Active debugging, when a scoped run can't explain a failure, or the failure points outside the changed area.
- Step 3 escalated and the user agreed.

"It's been a while" and "to be thorough" are not on this list.

## Do not

- Do not run tests for a merged or closed PR, or for a commit that's no longer the branch head.
- Do not update status reports, handoff notes, or anything else while tests run.
- Do not hide that a run was scoped. A green scoped run is not "all tests pass."
