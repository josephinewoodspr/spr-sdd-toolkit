---
name: log-correction
description: Record a correction - something an experienced engineer would have known and Claude got wrong - in the project's CLAUDE.md "Project corrections" section, and flag it if it should go back into the shared toolkit. Use when the user corrects a mistake and says to remember it, says "log this correction" / "add that to the mistakes list", or invokes /log-correction.
---

# Log Correction

Turn a correction into a lasting rule, so the same mistake doesn't happen next session, or on the next engagement.

## Invocation

`/log-correction [what went wrong]`. With no argument, use the most recent correction in the conversation, and confirm it in one line before writing.

## Steps

1. **State it as a rule, not a story.** One line that says what to do, followed by one line of why. Write "Kill test runs for a PR once it's merged; check PR state before running", not "Claude ran tests on a merged PR." If the rule is specific to this project (names a component, value, or client policy), keep it specific.
2. **Check for an existing rule.** If `CLAUDE.md` already says this, the problem is that the rule was ignored, not that it's missing. Tighten the existing wording (make it more specific, or move it up to a more prominent section) instead of adding a duplicate. Tell the user which one you did.
3. **Write it** under `## Project corrections` in the project's `CLAUDE.md`, with the date. If the section doesn't exist, create it at the end. If the rule clearly belongs in an existing section (Testing, Team mode, and so on), put it there and say so.
4. **Classify for the toolkit:**
   - **`toolkit-candidate`**: would apply to any engagement (process, git, testing, verification, communication). Mark the entry `[toolkit-candidate]`.
   - **project-only**: depends on this client's domain, stack, or policy. No mark.

   Don't decide that a candidate is client-free. Strip what you can, and a human confirms before anything goes into the shared toolkit.
5. Report the line you wrote, where it went, and its classification. Don't commit unless asked.

## Periodic harvest (when asked: "harvest corrections", "what should go back to the toolkit")

Collect every `[toolkit-candidate]` entry. For each one, propose where it belongs in the toolkit: a new skill, a clause in `templates/CLAUDE.md`, an edit to an existing skill, or drop it as not generalizable. Show each one with all client-specific detail removed. Nothing is copied into the toolkit without the user's explicit approval for each entry.

## Do not

- Do not log routine preferences or one-off instructions. A correction is something an experienced engineer would have known.
- Do not write client-specific content into the toolkit repo.
