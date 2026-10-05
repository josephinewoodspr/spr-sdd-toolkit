# Changelog

Record the version you vendor into a client repo so you can tell when it's stale. `/sdd-init` stamps the current version into the project's `CLAUDE.md` header.

## Unreleased

- `implement-spec`, `templates/specs/README.md`, `templates/specs/_template/design.md`: contract files hold no logic, and the implementation goes in a separate module that test agents never read. Delta mode now checks for this before running. This fixes a dual-blind breach where a delta-run test agent read the contract file and saw the implementation.
- `status-report` skill
- `scoped-tests` skill + `test-map-template.md`
- `git-hygiene` skill
- `legacy-archaeology` skill
- `team-review` skill
- `sdd-init` skill + `templates/CLAUDE.md` starter
- `templates/specs/` spec process, roadmap, + per-spec template
- `new-spec` and `implement-spec` skills
- `file-defect` skill + `templates/defects/`
- `log-correction` skill
- `templates/adr/`, `templates/metrics/`
