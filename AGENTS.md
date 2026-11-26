# AGENTS.md

Rules for AI coding agents working in this repository, whether alone or in parallel with other agents.
Read this file before starting any task. Project-specific details go in the **Project** section at the bottom.

---

## 1. Before you start

- Read the task end to end. If it's ambiguous and a wrong guess would be expensive, ask before writing code.
- Start from the latest base branch (`git fetch && git rebase origin/<base>`). Never start from a stale commit.
- Work in your **own branch and worktree**. One agent = one branch = one worktree. Never edit another agent's worktree.
- Write the plan as a checklist in `tasks.md` (or the task tracker you were given) and tick items off as you finish them.
- Keep scope tight: one feature or fix per branch. Note unrelated problems you find; don't fix them.

## 2. While working

- **Commit early and often.** Commit after every completed task, even as `wip:`. Uncommitted work is lost work.
- **Push your branch early** (a draft PR is fine) so the work doesn't live only on one machine.
- Follow the existing code style, structure and libraries. Don't add dependencies without saying why.
- Don't change shared files that other agents depend on (see **Hotspots**) unless you're their owner.
- Never commit secrets, `.env` files, credentials, or tool-generated noise (lockfile churn, editor files, local config rewrites).

## 3. Running checks

- **Run lint, typecheck and tests in the foreground. Never start them as background jobs and then wait for them.**
- **Always use a timeout** so a hung watcher or a missing mock can't block you forever:
  `timeout 600 <test command>`
- Run tests in non-watch / CI mode (`CI=1`, `--run`, `--watch=false`, etc.).
- Run the narrowest checks first (changed files), then the full suite before you open a PR.
- After you fix a failure, **re-run the checks** and confirm they pass. Don't report "fixed" without a passing run.

## 4. When stuck

- If the same check fails after **two** fix attempts, or you've made no progress for **15 minutes**: stop, commit what you have as `wip:`, and report what you tried and what's blocking.
- Never wait indefinitely on anything: a process, a background job, another agent, a network call.
- Don't "fix" a failing test by deleting it, skipping it or weakening its assertion unless the task says to.

## 5. Finishing

1. Rebase on the latest base branch and resolve conflicts.
2. Run the full checks (foreground, with a timeout) and confirm they pass.
3. Make sure `tasks.md` is fully ticked and matches what the code actually does.
4. Push and open a PR with: what changed, why, how it was tested, and any follow-ups.
5. Report back in this format:

```
Branch:        <name>
Base:          <commit>
Commits:       <n> (<one-line summary each>)
Uncommitted:   <none | list>
Checks:        <lint ✓/✗ · types ✓/✗ · tests ✓/✗>
Tasks:         <done>/<total>
PR:            <link | not opened, why>
Risks:         <migrations, shared files touched, open questions>
```

## 6. Rules for the orchestrator (when one agent launches others)

- **Run 2–4 agents at a time, not more.** Queue the rest and start the next one as each finishes.
- **Merge shared cleanups first**, then launch, so every agent starts from the same fresh base commit.
- **Assign hotspots before launching:** one owner per shared file (migrations, routers, root layout, nav, config). Others don't touch them.
- **Plan migration order up front.** Merge migration-adding branches one at a time and re-point each later migration onto the new head.
- Give each agent: the goal, the files it may touch, the base commit, the checks to run, and the report format above.
- **Stay light:** don't pull full agent logs into your own context. Read the final reports. Compact your context between batches.
- **Watch for stalls.** If an agent shows no progress (same elapsed time, same token count) for 15 minutes, stop it, check its worktree, and relaunch or finish it yourself.
- **Merge one PR at a time:** check CI, merge, rebase the remaining branches, repeat.
- Run long sessions inside `tmux` or `screen` so they survive a terminal crash.

## 7. Never

- Never force-push to shared branches (`main`, `dev`, release branches).
- Never merge your own PR unless you've been told to.
- Never run destructive commands (`rm -rf`, `DROP`, `reset --hard`, deleting branches/worktrees) without explicit approval.
- Never push to production or touch production data unless the task explicitly says so.
- Never send messages, emails or comments on anyone's behalf without showing a draft first.

---

## Project (fill in per repository)

```
Base branch:        dev
Install:            <e.g. pnpm install>
Lint:               <e.g. timeout 300 pnpm lint>
Typecheck:          <e.g. timeout 300 pnpm typecheck>
Tests:              <e.g. CI=1 timeout 600 pnpm test --run>
Run locally:        <e.g. docker compose up>
Migrations:         <tool + folder, e.g. alembic in backend/migrations>
```

**Hotspots** (shared files; only the assigned owner edits them):

- `<migrations folder>`
- `<root layout / app entry>`
- `<router / navigation>`
- `<shared config>`

**Files to never commit:**

- `<e.g. frontend/pnpm-workspace.yaml rewrites, .env*, local overrides>`
