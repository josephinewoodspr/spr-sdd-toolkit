# {PROJECT_NAME} — Specifications

<!-- Stamped from spr-sdd-toolkit. Owned by this project from here on - edit to fit. -->

This directory is **normative**: it defines what the system must do. Requirement documents and backlogs are *reference*. They feed specs, but they are never the requirement themselves. If code and an approved spec disagree, the spec is the source of truth. If the spec is wrong, fix it **in the same PR** as the code.

## Intake: all work enters here

Every request goes through one question: **what does the approved spec say?**

| Incoming | Route |
|---|---|
| New capability | `/new-spec <slug>` (next row of `ROADMAP.md`) → draft → approve → `/implement-spec NNN` |
| "It doesn't work properly" | File it: `/file-defect` → `defects/DEF-NNN`. No spec knowledge needed to file. Working it starts with **bug triage** (below) |
| Change to existing behavior | **Spec amendment** (below) → re-approve → implement the delta |
| Refactor, no behavior change | No spec change. Tests stay green. No AC may be touched |
| Cross-cutting decision | ADR in `{docs/adr/}` |

### Bug triage

Reproduce, then classify. The classification decides the fix path and whether the work is a defect or a change request:

- **CODE-WRONG**: behavior violates the spec. It's a defect *and* a test gap. Write a regression test from the spec and the bug report first, confirm it fails, then fix. The regression test stays, tagged to its AC.
- **SPEC-WRONG**: behavior *matches* the spec, but someone wants it different. Not a defect: it's a **change request**. Route it to a spec amendment.
- **SPEC-GAP**: the spec says nothing about the disputed behavior. Write the missing ACs (spec amendment), then implement.

### Spec amendment

1. Bump `rev:` and add a **Changelog** row: date, driver (the request, ticket, or spec that triggered it), ACs touched, and **what changed and why**. Another engineer or agent should understand the change from that row alone, without reading the diff. If another spec's work forced the amendment, name that spec.
2. **Never renumber ACs.** Changed behavior means you strike the old one (`~~AC-4 …~~ superseded by AC-9`) and add a new number. Retire the old AC's tests in the same change and cite them in the changelog. Never delete them silently.
3. Status returns to `Draft` and gets re-approved. Implementation then touches only what the changed ACs require. Tests for unchanged ACs must still pass.
   - A rev or AC number is final only once it reaches `{main}`. If `{main}` takes the same number first, the unmerged branch renumbers when it merges `{main}` in, and records the old → new mapping in the changelog.
4. If a change is so large the spec stops being readable, write a new spec and mark the old one `Superseded by NNN`.

## Layout

```
specs/
  NNN-short-slug/
    spec.md      # WHAT + WHY: user stories, scope, behavior, acceptance criteria
    design.md    # HOW: public contract, data contract, local design decisions
    tasks.md     # execution checklist + triage log + AC↔test traceability
  ROADMAP.md     # planned specs in order: the answer to "what's next?"
  _template/     # copy to start a new spec
```

Numbering is sequential and never reused. Reference the spec in every commit that implements it: `spec NNN: <message>`. A spec may be renumbered only while it is still a `Draft` that nothing references.

## Lifecycle

`Draft → Approved → In progress → Implemented` (also `Superseded by NNN`).

- **Draft**: being written. Anyone may edit.
- **Approved**: a human ({APPROVERS}) has reviewed it, and the contract files it declares are committed. Implementation may start only now.
- **In progress**: implementation or triage is underway.
- **Implemented**: every AC has a passing test, and the traceability table in `tasks.md` has no gaps.

**No coding session starts work without an Approved spec.**

## Writing a spec

- **User stories say who needs what and why.** Behavior and ACs make that precise. A spec is not pseudocode: describe what the system does and leave implementation choices to the implementer, within the architecture.
- **Acceptance criteria:** numbered `AC-1`, `AC-2`, …, each a single testable behavior with concrete values and explicit boundaries (`score == 58`, not "low"). If you can't name the assertion, it isn't an AC yet. ACs for business rules cite their source.
- **`high_stakes: true`** for anything where a wrong result causes real harm. Those specs get the strictest review (`/team-review thorough`) and their regression fixtures are never retired.

## Public contract

At approval, `design.md` declares the **contract**: the interfaces, the record types passed between components, and a factory or entry-point stub. The repo contains it, committed. Tests exercise the contract and fail until the implementation fills it in. Nothing about the implementation (internal names, structure, hints) appears in the contract or the spec.

## Implementation and triage

- Implementation runs through **`/implement-spec NNN`** (dual-blind): one isolated agent writes the tests and a separate isolated agent writes the implementation. Both work from the spec and contract alone, and neither sees the other's work. Amendments and bug fixes use its delta mode.
- **Tests win by default.** Classify every failure in `tasks.md`:
  - `IMPL-BUG`: the test matches the spec. Fix the implementation.
  - `TEST-BUG`: the test contradicts the spec. The triage entry quotes the spec line it violates. Fix the test.
  - `SPEC-AMBIGUITY`: both readings are defensible. Amend the spec in the same branch, then fix the side that loses. This is a finding, not a failure. Record it.
- Never weaken or delete a test to get to green.
- **Traceability gate:** every AC has at least one passing test tagged to it. Test names start with the AC id (`test_ac3_…`).
- A human reviews the triage log and traceability table before the branch is merged.
