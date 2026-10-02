# {PROJECT_NAME} — Delivery Metrics

**Purpose:** by the end of the engagement, have a defensible answer to *"what would this have taken built traditionally, compared with the agent-assisted approach we actually used?"*, plus a record of defects split by cause. Keep it updated as work lands, and summarize it at each milestone and at handoff. {If a case study is a deliverable, say so here.}

## Method: three tracks, compared honestly

1. **Contract envelope:** what was sold ({fee / duration / story points}), broken down by epic below. If the estimate already assumed agent-assisted delivery, then actual vs. envelope measures delivery against what was funded. It does **not** measure traditional vs. agentic.
2. **Modeled hand-build counterfactual:** estimated per spec when it closes, using the rubric below. This is the "everything written by hand" number. It is a **model, never a measurement**: always mark it ⚠ and show the component counts behind it, so anyone can audit or re-derive it.
3. **Agent-assisted actuals:** measured. Human active minutes, agent wall-clock, tokens.

**Hand-build rubric:** apply it to the merged diff when a spec closes. Assume one competent senior engineer, and count design, coding, debugging, self-review, and manual verification, not just typing. Fill in hours for this project's component types:

- {Component type, e.g. CRUD endpoint}: ~{N–M} h, including edge and error paths.
- {Integration point}: ~{N–M} h, including contract tests.
- Each test: ~{N–M} h, with fixtures amortized.
- {Anything else}: estimate individually and write down the reasoning.

Record the arithmetic in the row's note, not just the total.

## Contract envelope per epic

| Epic | Scope | Envelope |
|---|---|---|
| | | |

## Agent-assisted actuals, recorded per spec run

`/implement-spec` appends a row in its final phase:

- **Human active time:** minutes actually spent deciding and reviewing (spec review and approval, triage review, merge). Not time spent waiting.
- **Agent wall-clock** and **tokens:** from the run reports.
- **Triage counts:** IMPL-BUG / TEST-BUG / SPEC-AMBIGUITY, the workflow's internal quality signal.

## Defect categories

External reports from `defects/`, counted when each resolves:

- `implementation-bug`: the requirement was understood; the code is wrong (CODE-WRONG).
- `requirement-misunderstanding`: built differently than intended because the spec was wrong, silent, or ambiguous (SPEC-WRONG, SPEC-GAP, or an escaped SPEC-AMBIGUITY).

The claim to test over the long run: dual-blind implementation should catch code bugs before merge, so defects that escape should be mostly `requirement-misunderstanding`. No coding process can eliminate those; it can only surface them earlier.

| Category | Count |
|---|---|
| implementation-bug | 0 |
| requirement-misunderstanding | 0 |

## Per-spec run log

| Spec | Epic | Envelope | Modeled hand-build ⚠ | Human active | Agent wall-clock | Tokens | Triage: IMPL / TEST / AMBIG | Suite |
|---|---|---|---|---|---|---|---|---|
