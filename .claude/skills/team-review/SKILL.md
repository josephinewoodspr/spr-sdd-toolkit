---
name: team-review
description: Code review with team-standard settings - pinned effort level, High/Medium/Low severity, one comment per issue, isolated worktree - plus an opt-in multi-reviewer mode for high-stakes PRs. Use when asked to review a PR, branch, or diff on a team project, or when the user invokes /team-review.
---

# Team Review

A wrapper around the built-in `/code-review` so that every engineer on a project gets the same review, whoever runs it. Run-to-run differences between teammates usually come from setup, not the reviewer: one person's severity rules live in their personal memory, and another's effort level is left over from their last run. This skill pins those settings.

## Invocation

`/team-review <PR number | URL | branch> [thorough] [--comment]`

- *(default)*: one review at the pinned level.
- `thorough`: multi-reviewer mode (below). Use only when asked, or when the project's `CLAUDE.md` marks the change as high-stakes.
- `--comment`: post findings as inline PR comments, one per issue.

## Pinned settings

Always set these explicitly. Never rely on what a previous run or personal config left behind.

- **Effort level: `high`.** Always pass it explicitly, because `/code-review` otherwise reuses the last level typed. Lower levels report fewer, higher-confidence findings by design, which is why repeated rounds keep turning up new issues. A project can override the level in its `CLAUDE.md`. The point is that the whole team uses one level.
- **Isolation.** Review in a separate worktree, never by checking out the PR branch in the user's working checkout. Remove the worktree afterwards (see `git-hygiene`).
- **Scope.** The PR's diff against its base, plus whatever the diff directly breaks. Pre-existing issues in untouched code go in a separate, short "noticed, not blocking" list, if at all.

## Output conventions

- Every finding gets a severity:
  - **High:** wrong behavior, data loss, security, or a broken contract. Blocks merge.
  - **Medium:** likely bug in an edge case, a missing test for changed behavior, or a spec mismatch. Should fix before merge.
  - **Low:** clarity, naming, small simplification. Optional.
- One finding per issue. Each one has file:line, what's wrong, a concrete failure scenario for High and Medium, and a suggested fix.
- Order: High, then Medium, then Low. Cap Low at 5. If there are more, say how many were left out. A reviewer can always find another Low. Hitting the cap is not a reason for another round.
- Check the diff against its spec, if the project has one. A spec mismatch is a finding even if the code is otherwise clean.
- End with a verdict line: **Blocking** (any High), **Fix before merge** (any Medium), or **Approve**.

## Re-reviews

When reviewing again after fixes:

- Check each previous finding: fixed, partly fixed, or not fixed.
- Review the new changes at the same pinned level.
- Label any new finding in code the previous round already reviewed **missed in round N**. Don't drop it. These labels show whether the pinned level is high enough. If they keep appearing, raise the project's level.

## Thorough mode

For high-stakes changes only. It costs several times as much.

1. Spawn 3 review sub-agents in parallel, each with the full diff and the same conventions, each reviewing independently. Don't share findings between them.
2. Merge their findings: deduplicate by location and root cause.
   - Found by 2 or more reviewers: keep, high confidence.
   - Found by 1: verify it yourself against the code before keeping it. Drop it if you can't produce a concrete failure scenario.
3. Return one review in the standard format, noting for each finding how many reviewers found it.

## Do not

- Do not run without an explicit effort level.
- Do not post comments to a PR without `--comment` or an explicit request.
- Do not mix someone's personal review preferences into the review. If a preference should apply to the team, it belongs in the project's `CLAUDE.md`.
