# AGENTS.md template

A general-purpose `AGENTS.md` for repositories where AI coding agents work alone or in parallel (Claude Code, Codex, Cursor, and other tools that read `AGENTS.md`).

It sets rules that prevent the usual failures of multi-agent runs:

- agents stalling on background test runs that never return
- hours of uncommitted work living only in a worktree
- too many agents at once exhausting usage limits and bloating the orchestrator's context
- branches started from stale commits, and collisions on shared files and migrations
- vague status updates instead of a fixed report format

## Use it

1. Click **Use this template** (or copy `AGENTS.md` into your repo root).
2. Fill in the **Project** section at the bottom: base branch, check commands, migrations, hotspots, files to never commit.
3. For Claude Code, also reference it from `CLAUDE.md`:

   ```
   @AGENTS.md
   ```

## What's inside

| Section | Covers |
|---|---|
| Before you start | Fresh base, one branch/worktree per agent, task checklist |
| While working | Commit often, push early, stay in scope |
| Running checks | Foreground only, timeouts, non-watch mode, re-verify fixes |
| When stuck | When to stop and report instead of waiting |
| Finishing | Rebase, verify, PR, standard report format |
| Orchestrator rules | 2–4 agents at a time, hotspot owners, migration order, stall detection |
| Never | Hard limits on destructive and outward-facing actions |
| Project | Per-repo commands and shared files |

## License

MIT
