---
name: git-hygiene
description: Verified status of branches, worktrees, PRs, and background processes, plus a cleanup pass for abandoned ones. Use before claiming anything is merged, pushed, running, or stopped, when the user asks "what's open / what's running / where are we in git", before handing a repo to someone else, at end of day, or when the user invokes /git-hygiene.
---

# Git Hygiene

Two jobs. First: report what's true right now about branches, worktrees, PRs, and processes, from commands run now, not from memory. Second: clean up what's abandoned without destroying anyone's work.

## Invocation

- `status` (default): read-only. Report and change nothing.
- `cleanup`: run `status`, then propose a cleanup and act on what the user approves.
- `cleanup` plus explicit blanket approval ("just kill them, don't confirm"): skip per-item confirmation, but **only** for items in the *safe* class below. Anything at risk still gets asked about, one item at a time.

## Ground rule: no claim without a check

Before you state that something is merged, pushed, deleted, running, or stopped, run the command that proves it in the current turn and base the claim on its output. That applies here and in every other session.

- What you said earlier in the conversation doesn't count. Neither does what a sub-agent reported, or what the branch name suggests.
- If you can't check (no `gh`, no network), say "unverified" next to the claim.
- "No agents/processes running" means you listed processes and saw none. It doesn't mean the conversation hasn't started any recently.

## 1. Gather (quietly)

- **Branches.** Local branches, each with: upstream and ahead/behind counts, last commit date, and merge state.
  - Merge state has to survive squash and rebase merges, which `git branch --merged` doesn't detect. Check the PR first (`gh pr list --state all --head <branch>`). Without a PR, compare against the target branch: `git cherry`, or check whether the branch's diff is already in the target.
- **Worktrees.** `git worktree list --porcelain`. For each one: branch, uncommitted changes (`git -C <path> status --porcelain`), unpushed commits, disk size (`du -sh`), last modified. Also flag worktrees whose directory is missing (prunable).
- **PRs.** If `gh` is available: the current user's open PRs, with state, checks status, and whether the head matches the local branch.
- **Processes.** Long-running processes started from this repo or its worktrees: test runners, dev servers, watchers, language servers, build daemons, agent/CLI sessions. Report PID, command, working directory, and age. Find them by working directory (`lsof -d cwd` or `/proc/*/cwd`), not only by name. Count zombie or defunct processes separately.
- **Current checkout.** Which branch each terminal or worktree is on. Note if the main checkout is not on the branch the user last chose, because a background branch switch has bitten people before.

## 2. Classify

Sort every branch, worktree, and process into one of three classes:

- **Safe:** branch merged (verified per step 1) *and* no uncommitted changes *and* no unpushed commits. Also: worktrees whose directory is gone (prune only), and defunct processes.
- **At risk:** has uncommitted changes, unpushed commits, or an open PR. Also: a process whose owner you can't tell, or one that might be a teammate's or another session's.
- **Live:** what the user is working on right now. Never touch these.

When unsure, choose the more cautious class.

## 3. Report

Keep it short. One table per kind (branches, worktrees, PRs, processes), each with columns for the facts gathered and a class column. Then a totals line: N stale worktrees using X GB, N merged branches, N orphaned processes. Put anything surprising first: a PR you'd expect to be merged that isn't, a branch that diverged from its PR, a process running for a commit that no longer exists.

If there's nothing to clean up, say so in one line.

## 4. Cleanup (only in `cleanup` mode)

Present the proposed actions grouped by class, then act on approval:

- Worktrees: `git worktree remove <path>`, then `git worktree prune`. Never `--force` on a worktree with changes unless the user approved that specific path.
- Local branches: `git branch -d` (lowercase, so git refuses if unmerged). Use `-D` only for a branch verified merged via squash/rebase, or with explicit per-branch approval.
- Remote branches: never delete by default. Only on explicit request, and only for branches the current user authored.
- Processes: `SIGTERM` first, check again, and only escalate on approval. Never kill a process you can't tie to this repo.

Afterwards, re-run the relevant parts of step 1 and report what changed: what was removed, how much space was freed, and anything left behind (and why). Don't report success from the commands you issued. Report it from the re-check.

## Habits for every session (not only when invoked)

- Prefer a worktree to switching branches in a shared checkout. If you must switch branches, say so.
- When you finish with a worktree, background process, or dev server you started, remove or stop it before saying the task is done. If you're leaving it running on purpose, say so.

## Do not

- Do not delete, force-remove, or kill anything in the *at risk* class without approval for that specific item, even under blanket approval.
- Do not run cleanup as a side effect of another task.
- Do not trust branch names ("impl-done", "merged-…") as evidence of state.
