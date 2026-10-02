# Defects

One markdown file per defect: `DEF-NNN-short-slug.md`, numbered sequentially and never reused. This directory is where **anything reported as "not working as it should"** lands, whether the report comes from the client, a tester, or the team.

## Filing

Paste the report into any Claude session: *"file this defect: …"* (`/file-defect`). The session copies `_template.md` to the next `DEF-NNN`, keeps the **original report verbatim**, and fills in what structure it can. The reporter's words are evidence, so never paraphrase them away. Filing requires **no knowledge of specs**: reporters describe what they saw and what they expected, in their own terms.

Filing a defect is not working it. A defect stays `Reported` until someone says *"work DEF-NNN"*.

## Lifecycle

`Reported → Triaged → In progress → Resolved` (resolution: `fixed` | `works-as-specified` | `duplicate of DEF-NNN` | `withdrawn`).

## Triage (the first step of working a defect, done by the team, not the reporter)

1. Reproduce it, or record why it can't be reproduced.
2. **Map it to the governing spec and AC ids.** This happens now, not at intake. If no spec covers the behavior, that finding *is* the mapping (SPEC-GAP).
3. Classify per `specs/README.md`:
   - **CODE-WRONG**: violates the spec. Write a failing-first regression test, then fix via `/implement-spec NNN` in delta mode.
   - **SPEC-WRONG**: matches the spec, but the reporter wants different behavior. Route to a spec amendment (change-request territory). The resolution may be `works-as-specified` plus a linked amendment.
   - **SPEC-GAP**: the spec says nothing. Write the missing ACs (spec amendment), then implement.
4. Record all of it in the Triage section. The spec's changelog row cites `DEF-NNN` as its driver, and the defect's Resolution cites the spec rev and the regression test names. Links go both ways.
5. Set `category:`, exactly one per defect:
   - **`implementation-bug`**: the requirement was understood; the code is wrong (CODE-WRONG).
   - **`requirement-misunderstanding`**: the system was built differently than intended because the spec was wrong, silent, or ambiguous (SPEC-WRONG, SPEC-GAP, or an ambiguity that reached a reporter).

   Over an engagement, a spec-driven process should push escaped defects toward `requirement-misunderstanding`, with code bugs caught before merge. If `METRICS.md` exists, update its counts when the defect resolves.

## Severity

- `critical`: a wrong result in `high_stakes` behavior, or any exposure of client data. Work it before anything else. Its regression fixture is never retired.
- `major`: a feature is unusable and there's no workaround.
- `minor`: a workaround exists.
- `cosmetic`: appearance only.
