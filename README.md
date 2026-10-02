# SPR SDD Toolkit

The shared starting point for spec-driven development with Claude Code on SPR engagements: a set of skills, plus the templates that set up a project's working agreement with Claude. It collects what has worked across engagements, and guards against the failures that keep recurring, so you don't start from a blank CLAUDE.md or random advice from the internet.

Everything here is generic. Client specifics live in the client's repo, never in this one.

## What you get

- **A working agreement with Claude.** A starter `CLAUDE.md` with the defaults every engagement should have:
  - No database access outside a sandbox.
  - No opportunistic changes to teammates' work.
  - Verify before claiming anything is merged or running.
  - Scoped testing.
  - No narration while working.
- **A spec process.** Numbered specs with user stories and testable acceptance criteria. An intake route for every kind of request. Dual-blind implementation: tests and implementation are written by separate agents that never see each other's work. Triage where tests win by default, and traceability from each AC to its tests.
- **Skills for the recurring pain points:**
  - Test suites re-run for no reason.
  - Abandoned worktrees and zombie processes.
  - Claude being confidently wrong about state.
  - Inconsistent code reviews.
  - Status updates nobody can read cold.
  - Legacy systems nobody understands.

## Getting started

### 1. Get the toolkit

```bash
git clone https://github.com/josephinewoodspr/spr-sdd-toolkit.git ~/spr-sdd-toolkit
```

Keep it as a standalone checkout. Never nest it inside a client repo.

### 2. Kick off an engagement (once per project)

Start Claude Code in the toolkit and point `/sdd-init` at the project:

```bash
cd ~/spr-sdd-toolkit && claude
> /sdd-init ~/path/to/client-repo
```

It infers what it can from the repo, then asks you for the rest. It **always** asks about database access and any client policies; it never guesses those. Then, after confirming each piece, it sets up:

- `CLAUDE.md`, stamped with the toolkit version
- `specs/` (process README, roadmap, per-spec template)
- `defects/`, `docs/adr/`, and optionally `METRICS.md`
- `.claude/test-map.md` and a tracked `status-reports/` directory
- the toolkit skills, copied into the project's `.claude/skills/`

Nothing is committed. Review the files, fill in anything marked `TODO:`, and commit them yourself.

After this, the project owns its copies. The toolkit never syncs or overwrites them. Edit them to fit the client.

### 3. Work

Start Claude Code in the project repo as usual. The skills are now available there.

## Daily workflow

| When | Use |
|---|---|
| New capability | `/new-spec <slug>` → you review and approve → `/implement-spec NNN` |
| Someone reports a bug | `/file-defect <their words>`, then later "work DEF-NNN" |
| Change to existing behavior | Amend the spec (rev bump + changelog row) → `/implement-spec NNN` (delta) |
| Reviewing a PR | `/team-review <PR>` (add `thorough` for high-stakes changes) |
| Running tests | `scoped-tests` kicks in automatically: affected tests only, full suite before merge |
| "What's open / what's running?" | `/git-hygiene`, or `/git-hygiene cleanup` at end of day or before a handoff |
| Morning / end of day | `/status-report am` or `pm` |
| Claude got something wrong that an experienced dev wouldn't | `/log-correction` |
| Undocumented legacy system | `/legacy-archaeology <path>` |

## Skills

| Skill | What it does |
|---|---|
| `sdd-init` | One-time kickoff: stamps the templates into a project and vendors the skills. Never re-stamps. |
| `new-spec` | Scaffolds the next numbered spec and drafts it from the roadmap. Checks nobody else is already on it. Stops at Draft. |
| `implement-spec` | Dual-blind implementation of an Approved spec: an isolated test agent, then an isolated implementation agent. Then merge, triage, and AC-to-test traceability. Delta mode for amendments and bug fixes. |
| `file-defect` | Files a bug report verbatim into `defects/DEF-NNN`. Triages it against the spec only when asked to work it. |
| `team-review` | Code review with team-standard settings: pinned effort level, High/Medium/Low, one comment per issue, isolated worktree. `thorough` runs 3 independent reviewers and merges their findings. |
| `scoped-tests` | Runs only the tests affected by the change. Full suite only on request, pre-merge, or active debugging. Reports a terse summary. |
| `git-hygiene` | Verified status of branches, worktrees, PRs, and processes (no claim without a check), plus a cleanup pass that never touches at-risk work. |
| `status-report` | AM/PM report: decisions made, decisions pending, out-of-scope changes. Written to a tracked `status-reports/` directory, so teammates and other sessions can read it. |
| `log-correction` | Turns a correction into a rule in the project's CLAUDE.md and flags rules that should come back to this toolkit. |
| `legacy-archaeology` | Read-only, static reverse-engineering of a legacy system into data-flow, table-usage, business-rule, and open-question docs, as the basis for a modernization spec. |

## Templates

| Template | Stamped to |
|---|---|
| `templates/CLAUDE.md` | `CLAUDE.md`: guardrails, team mode, verification, testing, communication, specs, project corrections |
| `templates/specs/` | `specs/`: process README, `ROADMAP.md`, `_template/` (`spec.md`, `design.md`, `tasks.md`) |
| `templates/defects/` | `defects/`: intake process + `_template.md` |
| `templates/adr/` | `docs/adr/_template.md` |
| `templates/metrics/` | `METRICS.md`: envelope vs. modeled hand-build vs. agent-assisted actuals |
| `.claude/skills/scoped-tests/test-map-template.md` | `.claude/test-map.md` |

## Locked-down client environments

If the client environment can't reach this repo, `/sdd-init` vendors the skills: it copies them into the client repo, and nothing there depends on the network afterwards. To vendor by hand:

```bash
cp -r ~/spr-sdd-toolkit/.claude/skills/<skill> <client-repo>/.claude/skills/
```

Note the toolkit version you vendored (from `CHANGELOG.md`) so you can tell when it's stale. Check whether the environment can reach the repo at kickoff. Don't assume.

Later, for environments that can reach this repo live, we can add a plugin manifest for `/plugin install` with versions pinned per engagement. That's additive and needs no restructure.

## Contributing

- **No client-specific content, ever:** no client names, thresholds, domain vocabulary, or anything from a client's codebase. Check every addition against this rule.
- Good additions usually start as `[toolkit-candidate]` entries in a project's CLAUDE.md (via `/log-correction`). Strip the client details, then propose them here.
- Add a line to `CHANGELOG.md` for every change to a skill or template.
- Write skills and templates in the same style as the existing ones. Bullets, not essays. Every rule says why it exists.
