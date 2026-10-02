---
name: new-spec
description: Scaffold the next numbered spec folder from the template and draft it from the roadmap. Use when the user says "/new-spec <slug>", "create the next spec", or "start a spec for <topic>". Produces a Draft only - it never approves or implements (approval is the human's; implementation is /implement-spec).
---

# New Spec

Scaffold and draft the next spec. Writing a spec is authoring work and happens in the main session. It is not implementation.

## Invocation

`/new-spec <kebab-case-slug>`. With no slug, propose one from the top unclaimed row of `specs/ROADMAP.md` and confirm with the user.

## Steps

1. **Read the rules first:** `specs/README.md` and the Specs section of `CLAUDE.md`. If the project's conventions differ from these steps, the project wins.
2. **Number:** the highest existing `specs/NNN-*` prefix + 1, zero-padded to 3 digits. Never reuse or skip numbers.
3. **Scaffold:** copy `specs/_template/` to `specs/NNN-<slug>/`.
4. **Frontmatter:** `spec: NNN`, `title`, `status: Draft`, `rev: 1`, `owner` (the requesting developer), `sources` (from the ROADMAP row), and `high_stakes` (true if a wrong result causes real harm; see `specs/README.md`).
5. **Draft the content:**
   - `spec.md`: Why, User stories, Scope and Out of scope, Behavior, numbered ACs, Open questions. Draft ACs from the sources. Each AC is a single behavior with concrete values and explicit boundaries, and each business rule cites its source. Every user story maps to at least one AC.
   - `design.md`: the proposed public contract (interfaces, record types, a factory or entry-point stub), the data contract, and notes for test and implementation. **Don't write contract files yet.** They are written and committed at approval.
   - `tasks.md`: the standard checklist with NNN filled in.
6. **Check for collisions:** search open PRs and active branches for work on the same capability. If someone is already on it, stop and tell the user before drafting further.
7. **ROADMAP:** move the row into the Numbered table with status Draft. If the spec isn't from the roadmap, add a row and tell the user. New scope should be a deliberate choice.
8. **Stop at Draft.** Present the spec for human review, along with its open questions. Don't set it to Approved, don't write contract files, and don't start `/implement-spec`.

## Drafting rules

- **Don't restate values that live in config.** If the project keeps thresholds, limits, or model ids in versioned config, the spec names the config *key* and what it means, never a default value. A number in an AC that decides an outcome is either an illustrative fixture value (say so) or belongs in config.
- **Don't copy the prototype.** Existing or prototype code is a reference for *what* it does. Don't lift its structure or schema into the contract without checking it against the spec.
- **Respect the project's architecture rules.** If `CLAUDE.md` or a constitution assigns a responsibility to a specific component, a spec for a different component must not take it over.
