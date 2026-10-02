---
name: legacy-archaeology
description: Reverse-engineer an undocumented legacy system from its source - entry points, data flow, table usage, business rules, open questions - as the foundation for a modernization spec. Use when asked to document, explain, or map a legacy codebase, batch job, or system nobody fully understands, or when the user invokes /legacy-archaeology.
---

# Legacy Archaeology

Produce documentation of what an existing system actually does, good enough to write a modernization spec from without re-reading the code. The usual situation: one person understands it, they're leaving, and everyone is afraid to touch it. The output is a set of documents, not changes to the system.

## Invocation

Takes the path or name of the system to document (a directory, a job, a set of programs). If the scope isn't clear, ask what's in and out before reading. A whole monorepo is not a scope.

## Hard limits

- **Read-only, static.** Read source, SQL, schema DDL, job and scheduler definitions, config, and sample files that are already in the repo. Never connect to a database, run the system, or execute its scripts, in any environment, even if credentials are lying around. If something can only be answered from live data, it becomes an open question.
- **No changes to the legacy code.** Not even a comment or a formatting fix.
- **Never copy secrets into the docs.** Note that a credential exists and where (`config/db.ini`, key `password`), never its value.

## Method

Work in this order. Each pass feeds the next.

1. **Inventory.** List languages, file counts, and build or run mechanism. Find the entry points: scheduled jobs, main programs, triggers, endpoints, queue consumers. Note anything you can't parse.
2. **Control flow.** For each entry point, trace what calls what, down to where data is read or written. Note the trigger and schedule, if known.
3. **Data flow and table usage.** For each table, file, or external interface: who reads it, who writes it, which columns, and under what conditions. Include stored procedures, views, and dynamic SQL. Flag SQL assembled at runtime as only partly knowable.
4. **Business rules.** Turn the conditional logic into plain statements ("records with status X older than 30 days are excluded from the feed"). This is the most valuable output and the easiest to get wrong. Keep each rule tied to its source.
5. **Open questions.** Everything you couldn't determine, plus anything that looks like a bug, dead code, or an undocumented assumption. Phrase each one so the person who owns the system can answer it in a sentence.

For a large system, split by entry point or subsystem and document each separately (in parallel if sub-agents are available). Then do one pass over all of them to reconcile shared tables and cross-job dependencies.

## Evidence rules

- Every claim cites its source: `file:line` or `procedure_name`.
- Label each claim **confirmed** (read directly in code) or **inferred** (a reasonable reading that isn't certain). Never present an inference as fact. A modernization built on a wrong guess about a legacy rule is the failure this skill exists to prevent.
- If two places in the code disagree, document both and raise an open question. Don't pick one.

## Output

Write to `docs/legacy/<system-name>/` in the repo the work is for (create it if needed):

- `overview.md`: what the system is for, in two paragraphs a non-engineer can read. Entry points, schedule, inputs, outputs. A diagram of the overall flow if it helps.
- `data-flow.md`: per entry point, the path from trigger to final output.
- `table-usage.md`: a matrix with tables, files, and interfaces as rows and programs or procedures as columns. Each cell says R, W, or RW, with the columns touched.
- `business-rules.md`: numbered rules, each with source and confirmed/inferred.
- `open-questions.md`: numbered, each with why it matters and who could answer it. Add answers here as they come in, and update the other docs to match.

Number rules and questions stably (`BR-12`, `Q-7`) so specs and conversations can refer to them.

## Feeding the modernization spec

When the user moves to the rebuild, write requirements as functional behavior and user stories based on `business-rules.md` ("the feed excludes X because BR-12"), not as a translation of the old code. The new system should do what the old one did for the business, not repeat how it did it. Resolve every open question that affects a requirement before that requirement goes into the spec.

## Do not

- Do not "improve" a rule while documenting it. Write down what the code does, even if it looks wrong, and raise the doubt as an open question.
- Do not summarize away edge cases. In legacy systems, the edge cases are usually the reason the code exists.
