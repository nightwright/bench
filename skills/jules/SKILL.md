---
name: jules
description: Dispatch operator for Jules (jules.google.com) — Google's async coding agent. Use when handing bounded, verifiable work to Jules via its API or web UI, reviewing its PRs, or checking task/quota status. Knows what Jules can and can't do, the prompt recipe that works, and the follow-up-commit trap.
triggers:
  - user
  - model
allowed-tools:
  - read
  - exec
  - grep
---

# Jules — dispatch operator for Google's async coding agent

Jules (jules.google.com) connects to GitHub repos. It clones the repo into
a Google Cloud Linux VM, plans, implements on a branch, and opens a PR.
Use it for bounded, verifiable work — treat its PRs like a junior
contributor's: always review the diff before merging.

## Project context

You work on whatever project you're invoked in. What's connected, what the
env setup looks like, what Jules can/can't do *here*, and the standing
backlog source all live in `.devin/skills/jules/FINDINGS.md` under
`## Project context` — read it before dispatching. The project's
`AGENTS.md` conventions bind Jules's output too (Jules reads it); a PR that
violates them is a reject reason.

## What Jules is good for

- **Browser/Linux-testable work** — unit and E2E suites that run in a
  Linux VM once the env snapshot is set.
- Small features, bug fixes, test coverage, refactors — bounded tasks with
  a verifiable end state.
- Anything where "review the PR" is an acceptable acceptance test.

## Hard limits

- **No platform builds it can't run.** Jules's VM is Linux — it cannot
  build or verify Windows/macOS-only toolchains, mobile builds, or
  platform-native targets. Check the project's `## Project context` for
  the specific off-limits areas; never give it those tasks — it would be
  writing blind.
- **Quota:** free tier is ~15 tasks/day, ~3 concurrent. A "task" = one
  New job. In-session chat to refine the plan doesn't consume tasks.
- Code is read by Google's VMs. Only point it at repos with no secrets —
  and never paste secrets into a Jules prompt either.

## Environment setup

Each connected repo has an Initial Setup script in Jules → repo →
**Environment** (typically: install deps, install test browsers, run a
smoke spec). The project's exact script lives in
`.devin/skills/jules/FINDINGS.md`. If a task fails with missing-dependency
errors, the snapshot is stale — re-run it after dependency changes land.

## Task workflow

1. **New** → repo + base branch → paste a bounded prompt → choose
   **Review** (plan approval on; "Start" skips it — only for trivial
   tasks). Or dispatch via the API with `requirePlanApproval: false` +
   `AUTO_CREATE_PR` for fully unattended runs.
2. Read the plan. Reject or adjust if it plans to touch files outside
   scope, restyle, or add dependencies.
3. It works async → branch + PR on GitHub → review diff → merge or
   close. Nothing reaches the base branch without a merge.

   **Watch out — the follow-up-commit trap:** Jules may push follow-up
   commits onto an already-merged/closed branch instead of opening a new
   PR — the work then sits invisible (no open PR). When it reports done
   but `gh pr list` is empty, run `git fetch` and check
   `git log --oneline main..origin/<its branch>`, then cherry-pick the
   delta. To prevent it, end prompts with "Open a new pull request for
   this change."

## Prompt recipe that works

Each prompt must be self-contained — Jules gets fresh context:

- The **exact files** likely involved
- The **behavior change**, not just the bug name
- A **verification command** scoped to what its VM can run (e.g.
  `npx playwright test <spec> --project=chromium`)
- Reminder to follow existing code/test style
- "Open a new pull request for this change." at the end

## Off-limits by default

- Anything the VM can't build/verify (see project context for specifics)
- Secret handling, credential flows beyond test fixtures
- Deploys and CI/CD changes — never let Jules add workflows or deploy
  config unless explicitly asked

Docs: https://jules.google/docs — env setup, limits, REST API, and the
`jules-invoke` GitHub Action for scheduled/triggered tasks.

## Status reporting

If the project has a status/count mechanism (script or API key), report
`Jules: Task N of 15` on every dispatch/status reply. **Caveat:** the API
only returns API-created sessions — tasks created at jules.google.com
don't appear, so the count is only accurate if all dispatch goes through
the API.
