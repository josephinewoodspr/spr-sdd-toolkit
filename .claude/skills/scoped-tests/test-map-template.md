# Test Map

<!-- Copy to `.claude/test-map.md` in the project repo and fill in. This file is
     project-specific: it belongs in the client repo, never in the toolkit. The
     scoped-tests skill reads it before anything else. Delete sections that
     don't apply. -->

## Commands

| Suite | Command | Typical duration | Shared state? |
|-------|---------|------------------|---------------|
| {unit} | `{command}` | {~1 min} | {none} |
| {api / integration} | `{command}` | {~20 min} | {shared DB — never run concurrently} |

**Scoped selector syntax:** {how to run a subset, e.g. `--filter`, a path argument, `-k`}

## Path → tests

| Changed path (glob) | Run |
|---------------------|-----|
| `{src/feature/**}` | `{tests/feature/**}` |
| `{api/**}` | {api suite, scoped by controller} |

## Always escalate to full suite

- `{path or file}` — {why: e.g. shared fixtures, schema/migrations}

## Never needs tests

- `{docs/**}`, `{*.md}`
