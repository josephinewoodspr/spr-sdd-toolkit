---
spec: NNN
title: <short name>
status: Draft            # Draft | Approved | In progress | Implemented | Superseded by NNN
rev: 1                   # bump on every amendment; add a Changelog row
owner: <who approves>
high_stakes: false       # true if a wrong result causes real harm (money, data, safety) - stricter review
sources: <requirement docs, backlog/ticket IDs, related specs, existing-code refs>
---

# Spec NNN — <Title>

## Why
<1–3 sentences: the need this serves and where it fits in the plan.>

## User stories
<Who needs what, and why. These describe the behavior from the user's point of view. The ACs below make it testable.>

- As a <role>, I want <capability>, so that <outcome>. (→ AC-1, AC-2)

## Scope
<What this spec covers. Bullet list.>

**Out of scope:** <explicitly excluded, with the spec number that will cover it if known.>

## Behavior
<Prose and tables describing required behavior. Use the project's domain vocabulary exactly. Inputs and outputs as record shapes. Decision rules as tables with concrete values. Describe what the system does, not how to build it.>

## Acceptance criteria

- **AC-1**: <single testable behavior, concrete values, explicit boundaries. Cite the source for any business rule.>
- **AC-2**: …

## Open questions
<Anything unresolved. An Approved spec may carry open questions only if no AC depends on them.>

## Changelog

| Rev | Date | Driver | ACs touched | What changed and why |
|---|---|---|---|---|
| 1 | <date> | Initial | AC-1 … AC-n | — |
