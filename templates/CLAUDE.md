<!-- Stamped from spr-sdd-toolkit {TOOLKIT_VERSION} on {DATE}. This file is now
     owned by this project. Edit freely. It is never synced back or overwritten
     by the toolkit. -->

# {PROJECT_NAME}

{One or two sentences: what this system is and who it's for.}

- **Stack:** {languages, frameworks, infra}
- **Specs live in:** {path or tool, e.g. `specs/`, Confluence space}
- **Main branch:** {main}

## Data and credential access (non-negotiable)

- Never connect to a database in any environment except the designated sandbox: {SANDBOX_DB, or "none, no DB access at all"}. Never production, never a shared or pre-demo environment.
- Don't use credentials you happen to find (env vars, config files, keychains, another tool's tokens) for anything the task didn't explicitly authorize.
- Changes to shared environments go through scripts or migrations that a human reviews and runs. Write them; don't run them.
- Never create or change anything that makes a resource reachable from outside (public buckets, open tables, permissive CORS or ACLs, exposed ports) without explicit approval for that specific change.

## Team mode

This codebase has several people, and several Claude sessions, working at once. Sessions don't share context, so don't assume you know what others are doing.

- Stay inside the task's scope. If you notice a bug, cleanup, or improvement outside it, list it at the end. Don't fix it.
- Before touching code outside the files the task names, check whether someone else owns it: open PRs, active branches, the spec's assignee. If it's unclear, ask.
- Before implementing a spec, check that it isn't already implemented or in progress on another branch or PR.
- Never switch branches in a shared checkout. Use a worktree.
- Before working on a PR's branch, read its comments (`gh pr view <N> --comments`). Review notes and decisions between teammates often live only there.

## Verify before you claim

- Don't say something is merged, pushed, deployed, passing, running, or stopped unless a command run in this turn shows it. Otherwise say "unverified." (`/git-hygiene`)
- When you finish, stop any background processes and remove any worktrees you started, unless you say you're leaving them on purpose.

## Testing

- Run only the tests affected by the change. Full suite only on explicit request, pre-merge, or active debugging. (`/scoped-tests`, `.claude/test-map.md`)
- Summarize test output: counts, failures with file:line. Don't paste logs.

## Communication

- Don't narrate routine steps or restate tool output. Speak up mid-task only to ask a blocking question, flag something unexpected that changes the plan, or give a one-line milestone on long work. Otherwise report at the end: what changed, what's left, and what needs a human decision.
- Don't produce status reports, handoff notes, or summaries unless asked. (`/status-report`)

## Specs

- All product code implements a numbered spec in `specs/` with `status: Approved`. New specs start with `/new-spec`. Implementation goes only through `/implement-spec` (dual-blind). Never write a spec's tests and its implementation in the same context. No approved spec means write and approve the spec first. Process, intake, and triage rules: **`specs/README.md`**.
- A spec covers what and why: user stories, scope, behavior, and acceptance criteria with concrete values. It is not pseudocode. Implementation choices belong to the implementer, within the architecture.
- All incoming work (features, bug reports, change requests) goes through the intake table in `specs/README.md`. A bug is first triaged against the spec: CODE-WRONG, SPEC-WRONG, or SPEC-GAP.
- **Amendments:** bump `rev`, never renumber ACs, and add a changelog row saying what changed, why, and which request or spec triggered it. Another agent should understand the change from that row alone, without reading the diff.
- When someone pastes a bug report, file it with `/file-defect`: keep the report verbatim, and leave spec and classification empty. Filing a defect and working it are separate steps.
- Cross-cutting decisions get an ADR in `{docs/adr/}`. ADRs are numbered and immutable; supersede an ADR rather than editing it.
- Tests win by default. A test changes only by citing the spec line it contradicts. Never weaken or delete a test to get to green.

## Project corrections

Things an experienced engineer on this project would know and Claude has gotten wrong. Add an entry each time you correct it (`/log-correction`). Review these periodically: some generalize back to the toolkit.

- {none yet}
