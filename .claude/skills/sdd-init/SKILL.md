---
name: sdd-init
description: One-time engagement kickoff - stamp the SPR CLAUDE.md starter template into a project repo, fill in project specifics, and set up the supporting files. Use only when the user explicitly asks to initialize, bootstrap, or kick off SDD setup on a repo, or invokes /sdd-init.
---

# SDD Init

Stamp the toolkit's CLAUDE.md starter into a project once, at engagement kickoff. After that the file belongs to the project. This skill never syncs, upgrades, or overwrites it later.

## Invocation

`/sdd-init [target-repo-path]`. Defaults to the current repo.

## Find the template

The template is `templates/CLAUDE.md` at the root of the spr-sdd-toolkit checkout, three levels up from this skill's directory. If it isn't there (for example, only the skill folder was vendored), stop and tell the user to copy `templates/` from the toolkit alongside it. Don't reconstruct the template from memory.

## Steps

1. **Check for existing setup.**
   - If the target has a `CLAUDE.md` with the toolkit's "Stamped from spr-sdd-toolkit" header, it's already initialized. Stop. Offer to diff it against the current template and *propose* additions, which the user applies by hand or approves one by one. Never re-stamp.
   - If it has a `CLAUDE.md` without the header, don't replace it. Propose merging the template's sections into it, keeping every existing line, and show the result before writing.
2. **Fill placeholders.** Infer what you can from the repo: project name, stack, spec location, main branch. Ask the user only for what you can't infer. Always ask for these, never guess: the sandbox database (or confirmation that there's no DB access at all), and any client-specific policies that must go in on day one.
3. **Stamp.** Write `CLAUDE.md` with `{TOOLKIT_VERSION}` (from the toolkit's `CHANGELOG.md`: the latest version, or `unreleased` plus the date) and `{DATE}` filled in. Delete template braces that have no answer yet, but keep a `TODO:` for each one so the gap stays visible.
4. **Supporting files** (ask before each):
   - `specs/README.md`, `specs/ROADMAP.md`, and `specs/_template/` from `templates/specs/`, if the project has no spec process yet. If it has one, don't replace it. Point out what the toolkit's version covers that theirs doesn't (intake table, bug triage classes, amendment rules, traceability) and let the user choose.
   - `defects/README.md` and `defects/_template.md` from `templates/defects/`.
   - `docs/adr/_template.md` from `templates/adr/`, unless the project already keeps ADRs somewhere.
   - `METRICS.md` from `templates/metrics/`, if the engagement needs a delivery comparison or case study. Ask where it should live.
   - `.claude/test-map.md` from `scoped-tests/test-map-template.md`, if the project has a test suite worth scoping.
   - An empty `status-reports/` directory, tracked in git (add a `.gitkeep`).
   - Vendor toolkit skills into `.claude/skills/` if the environment can't install the toolkit as a plugin. Record which version you vendored.
5. **Report.** List what was created, the placeholders still marked `TODO:`, and a reminder that nothing was committed.

## Do not

- Do not overwrite or re-stamp an initialized `CLAUDE.md`.
- Do not copy client-specific content back into the toolkit. Answers given here stay in the project.
- Do not commit. The user reviews and commits the stamped files.
