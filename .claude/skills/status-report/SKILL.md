---
name: status-report
description: Generate an AM or PM status report - decisions made, pending decisions, and out-of-scope changes since the last report. Use when the user asks for a status report, a daily/standup update, "where are we", or invokes /status-report.
---

# Status Report

Produce a short report for a human catching up cold - not a narration of what you did or how you did it. If a teammate (or their own Claude session) reads only this file, they should understand where things stand without reading the diff or the transcript.

## Invocation

Takes an optional argument: `am` or `pm`. If omitted, infer from local time (before 12:00 -> `am`, otherwise `pm`). This only changes the label, not the content.

## Gather inputs (quietly - don't narrate this part)

1. **Prior report.** Look in `status-reports/` for the most recent file. If one exists, read it first. Carry forward anything still pending; mark it resolved if it's been settled since, don't just drop it.
2. **Git activity since the prior report** (or since start of day if there isn't one): recent commits, current branch state, `git status`. If this is a GitHub repo and `gh` is available, check open PRs authored by the current user.
3. **This session's context.** Look back through the conversation (and any handoff notes) for:
   - **Decisions made** - an ambiguity that got resolved, a judgment call taken. One line each: what was decided, why.
   - **Decisions pending** - anything flagged as needing a human call, blocked on a client answer, or deliberately left open.
   - **Out-of-scope changes** - anything done beyond the original ask: an opportunistic fix, a scope change, a spec revision this work triggered. Call these out explicitly - they're the easiest thing for a reader to miss if buried in routine progress notes.

## Write the report

Follow the shape in `report-template.md`. Bullets, not paragraphs. No restated code, no step-by-step account of tool calls. If a section is empty, write "None" rather than omitting it - an empty section is a signal, not noise.

Save it to `status-reports/<YYYY-MM-DD>-<am|pm>.md` (create the directory if it doesn't exist) and also show it in the reply. This directory should be committed to the repo, not gitignored - the point is that a teammate, or another Claude session on the same project, can read it without you.

## Do not

- Do not generate or update a report except when this skill is explicitly invoked. Never append to one mid-session or after routine actions - it's a checkpoint, not a live log.
- Do not include anything that isn't a decision, a pending item, or an out-of-scope change. If it's just "what happened," it belongs in the commit history, not here.
