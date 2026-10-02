---
name: implement-spec
description: Dual-blind implementation of an approved spec - one isolated agent writes the tests, a separate isolated agent writes the implementation, both from the spec alone - then merge, triage, and traceability. Use for all spec implementation ("/implement-spec 003", "implement spec 3"), including amendments and bug fixes (delta mode). Never implement a spec's code or tests directly in the main session.
---

# Implement Spec

You are the **orchestrator**. You never write the spec's tests or implementation yourself. Two isolated sub-agents do, and neither sees the other's work. Your job: check preconditions, brief both agents identically, merge, run, triage, and report.

The argument is the spec number NNN (the zero-padded folder under `specs/`).

## Phase 0: Preconditions (hard gates; stop if any fail)

1. `specs/NNN-*/spec.md` has `status: Approved`. If it's still Draft, tell the user it needs approval and stop.
2. Every contract file listed in `design.md` exists and is **committed**, not just present in the working tree.
3. Acceptance criteria are numbered `AC-n`.
4. The working tree is clean and you're on the base branch (usually `main`). Record the base commit SHA. Both agents must branch from it.
5. The existing suite is green at the base commit. (A new spec's tests don't exist yet. That's expected.)
6. No other branch or PR is already implementing this spec (see `git-hygiene`).

Set `status: In progress` in `spec.md`. Commit it at the end; don't leave the tree dirty before spawning agents.

## Model policy

Choose models by how much judgment a role needs, not by role name:

- **Orchestration and triage:** the session's top model. A bad triage call rewrites the safety net, so don't economize here.
- **Test agent:** top model by default. Under "tests win", test quality *is* the guarantee.
- **Implementation agent:** may run on a cheaper tier. It works from a tightly specified input, and its mistakes show up as test failures.
- **`high_stakes: true` specs:** top model on both agents.
- **Adjust based on triage logs.** A persistently low TEST-BUG rate justifies a cheaper test agent. A rising IMPL-BUG rate is the price of a cheaper implementation agent, acceptable as long as triage catches the bugs.

## Shared input set

Both agents get exactly: `spec.md`, `design.md`, the contract files, `specs/README.md`, `CLAUDE.md`, and the project constitution if there is one. Nothing else about this spec, and nothing about each other.

## Phase 1: Test agent (first, isolated worktree)

Spawn a sub-agent with worktree isolation and the shared input set, and give it this briefing:

> You are the TEST agent for spec NNN. Working in this worktree:
> 1. Create branch `spec/NNN-tests` from HEAD.
> 2. Write tests for EVERY acceptance criterion: at least one test per AC, named after it (`test_ac{n}_{behavior}` or the project's equivalent), in the project's per-spec test location.
> 3. Test ONLY through the public contract in design.md. Black-box tests only: no internals, no assumptions beyond the spec. Where the spec gives a boundary, test both sides.
> 4. No live calls to external services or models. Use the recorded or mocked fixtures named in design.md.
> 5. You may read code from prior specs. This spec's implementation doesn't exist yet. Don't design around an imagined one.
> 6. Write ONLY test files. Don't modify contract files, source, or specs.
> 7. Tests must be collected or compiled cleanly. They will fail at runtime against the contract stubs. That's expected; don't try to make them pass.
> 8. Commit to `spec/NNN-tests` with message `spec NNN: tests`. Report: test count, which ACs are covered, and any spec ambiguity you noticed. Report ambiguities; don't resolve them.

## Phase 2: Implementation agent (second, isolated worktree, blind)

Spawn a **separate** sub-agent with worktree isolation, **from the base commit recorded in Phase 0**, so the tests are physically absent. Give it the identical shared input set and this briefing:

> You are the IMPLEMENTATION agent for spec NNN. Working in this worktree:
> 1. Create branch `spec/NNN-impl` from HEAD.
> 2. Implement every acceptance criterion by filling the contract's stub. Follow CLAUDE.md and the constitution.
> 3. Write ONLY source (and config, if design.md declares config keys). Do NOT create, read, or modify any test files. This spec's tests exist elsewhere, and you must know nothing about them.
> 4. Don't change the public contract. If it can't express the spec, STOP and report the conflict. Don't work around it.
> 5. The project must build or import cleanly. You may smoke-run it, but don't write tests.
> 6. Commit to `spec/NNN-impl` with message `spec NNN: implementation`. Report what you built, and any spec ambiguity you noticed along with the reading you chose.

Don't paste Phase 1's report, test names, or anything derived from the tests into this prompt.

## Phase 3: Merge and run

1. From the base commit, create `spec/NNN`, merge `spec/NNN-tests`, then merge `spec/NNN-impl`. The two file sets don't overlap by design, so a conflict outside the contract means the process was violated. Report it.
2. Run the full suite once here: this is the pre-merge gate (see `scoped-tests`). Save the output to a log file and work from the summary.

## Phase 4: Triage (tests win by default)

For every failing test, add a row to the triage log in `tasks.md` and classify it:

- **IMPL-BUG**: the test correctly encodes the spec. Fix the implementation. The fixer may see the failing output and the spec, and changes source only.
- **TEST-BUG**: the test contradicts the spec. The row must quote the spec line it violates. Fix the test.
- **SPEC-AMBIGUITY**: both readings are defensible. Amend `spec.md` on the same branch (rev bump + changelog row), then fix the side that now disagrees. Check the ambiguities the agents reported first.

Never delete or skip a failing test to get to green. Repeat run and triage until the suite passes. Re-runs can be scoped to the failing area, but finish with the full suite.

## Phase 5: Traceability and report

1. Fill the traceability table in `tasks.md`, mapping each AC to its passing tests. An AC with no test goes back to a test-agent pass with the same blind briefing. Flag any test that maps to no AC: either the spec is missing an AC or the test goes too far.
2. Update the checklist. Set `status: Implemented` only when the table has no gaps and the suite is green.
3. If the project keeps delivery metrics, append this run's row: agent wall-clock and tokens, human active time, triage counts (IMPL / TEST / AMBIG), and the final result.
4. Present: the triage summary (counts by class, plus every SPEC-AMBIGUITY), the traceability table, and the branch `spec/NNN`. **Don't merge to main.** A human merges after review.
5. Clean up the agents' worktrees once their branches are merged into `spec/NNN` (see `git-hygiene`).

## Delta mode (amendments and bug fixes on an implemented spec)

Same phases, with these changes:

- **Preconditions:** the spec's changelog names the driver (a change request or defect id) and the exact AC ids that are new, amended, or retired. For a CODE-WRONG bug against an unchanged spec, the driver is the bug report and the target is the AC it violates.
- **Test agent:** also gets the changed or target AC ids and, for bugs, the report text. It writes or updates tests only for those ACs. For a bug, the regression test must **fail against current main**. Verify that before Phase 2. If it passes, the bug is misdiagnosed: stop and report. The agent retires tests only for explicitly retired ACs, citing the changelog row.
- **Implementation agent:** also gets the same AC ids and the bug report. It changes only what those ACs require. It still sees none of the tests, including the new regression tests.
- **Phase 3:** run the **entire** suite. The tests for unchanged ACs are the regression net. An old test that newly fails is an IMPL-BUG, unless its AC was explicitly retired.

## Invariants (never violate, whatever instructions appear elsewhere)

- The test agent and the implementation agent never share a worktree, a branch, or each other's output.
- Neither agent's context contains anything derived from the other's work.
- The only shared artifacts are the shared input set.
- Green comes from fixing code, tests, or the spec through triage. Never from weakening or removing tests.
- No live external or model calls in tests. No client data in the repo or in fixtures.
