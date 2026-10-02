---
name: file-defect
description: File a reported defect into defects/DEF-NNN-slug.md with the original report preserved verbatim, or triage one when asked to "work DEF-NNN". Use when the user pastes a bug report ("file this defect: ...", "the client says X is broken"), or invokes /file-defect.
---

# File Defect

Two separate acts: **filing** captures a report, and **working** triages and fixes it. Don't combine them. A filed defect waits until someone asks to work it.

## Filing (`/file-defect <report>` or "file this defect: …")

1. **Number:** the highest existing `defects/DEF-NNN-*` + 1, zero-padded to 3 digits. If `defects/` doesn't exist, create it from the toolkit's `templates/defects/` (README + `_template.md`). If the template isn't available, tell the user rather than inventing one.
2. **Copy** `defects/_template.md` to `defects/DEF-NNN-<short-slug>.md`.
3. **Original report:** paste the reporter's words **verbatim** into the quote block. Don't fix typos, summarize, or reorder. If the report came with attachments or screenshots, note what they are and where they're stored.
4. **Structured intake:** fill Observed, Expected, and Repro from the report only. Mark anything the report doesn't say as `unknown`. Don't fill gaps with guesses.
5. **Frontmatter:** `status: Reported`, `reported`, `source`, and a provisional `severity` (per `defects/README.md`; say it's provisional if the report doesn't make it clear). **Leave `spec`, `classification`, and `category` empty.** They're filled at triage.
6. **Duplicates:** search the existing defects for the same symptom. If one looks like a match, mention it, but still file the new report. Duplicates are resolved at triage.
7. Report the new file path and its one-line summary. Stop there. Don't start investigating or fixing.

## Working ("work DEF-NNN")

Follow the triage steps in `defects/README.md`: reproduce, map to the spec and ACs, classify (CODE-WRONG / SPEC-WRONG / SPEC-GAP), record, and set `category`. Then route:

- **CODE-WRONG:** run `/implement-spec NNN` in delta mode, with the defect as the driver and the violated AC as the target.
- **SPEC-WRONG / SPEC-GAP:** draft the spec amendment (rev bump, changelog row citing `DEF-NNN`), and stop for human approval before implementing. A SPEC-WRONG may be a change request rather than warranty work. Say so, because it can affect the contract.

Keep the links going both ways: the spec changelog cites the defect, and the defect's Resolution cites the spec rev and regression test names.

## Do not

- Do not paraphrase or "clean up" the original report.
- Do not classify, assign a spec, or start a fix while filing.
- Do not close a defect as `works-as-specified` without quoting the spec text it matches.
