# Parallel Workflow Skills for Claude Code

Coordinate multiple Claude Code sessions working in parallel on the same codebase using git worktrees.

## What This Is

Three Claude Code skills that work together:

1. **parallel-orchestrator** - Splits work into parallel workstreams, creates worktrees, generates worker prompts
2. **parallel-worker** - Executes assigned tasks, commits with status prefixes, signals blockers
3. **parallel-retrospective** - Analyzes completed workflows, identifies improvements

## Why Use This

When you have a large task that could be split across multiple Claude sessions:
- Code review with 20 issues to fix
- Feature that spans backend, frontend, and tests
- Refactoring that touches independent modules

Instead of working sequentially, spawn multiple Claude sessions that work in parallel, each in its own git worktree. They communicate through git commits using standardized prefixes.

## How It Works

```
1. ORCHESTRATOR analyzes work items
   - Parses input documents (code review, issue list, etc.)
   - Estimates complexity, discovers dependencies
   - Proposes worker split, creates worktrees
   - Generates kickoff prompts for each worker

2. WORKERS execute in parallel (separate Claude sessions)
   - Each works in isolated worktree
   - Commits with prefixes: [CHECKPOINT], [BLOCKED:reason], [NEEDS:task-X/item], [COMPLETE]
   - Signals dependencies through commits

3. ORCHESTRATOR monitors and integrates
   - Parses worker commits for status
   - Coordinates cross-dependencies
   - Merges branches, resolves conflicts

4. RETROSPECTIVE analyzes (optional)
   - Reviews commit history
   - Evaluates planning decisions
   - Recommends improvements
```

## Quick Start

See [INSTALL.md](INSTALL.md) for installation.

### Basic Usage

```
You: I have code_review.md with 15 issues to fix. Use the parallel-orchestrator
     skill to split this into parallel workstreams.

Claude: [Analyzes document, proposes 3-4 workers, creates worktrees,
        generates kickoff prompts]

You: [Copy each kickoff prompt into a new Claude session]

Workers: [Each executes their tasks, commits progress]

You: Check on worker progress.

Claude: [Parses commits, shows status report]

You: All workers complete. Integrate the changes.

Claude: [Merges branches, resolves conflicts, cleanup]
```

## Commit Conventions

Workers use these prefixes for orchestrator-parseable status:

| Prefix | Meaning |
|--------|---------|
| `[CHECKPOINT]` | Subtask complete, continuing |
| `[BLOCKED:reason]` | Cannot proceed |
| `[NEEDS:task-X/item]` | Need output from another worker |
| `[COMPLETE]` | All tasks finished |

## Naming Conventions

| Item | Pattern |
|------|---------|
| Worktree | `worktrees/task-<id>-<description>/` |
| Branch | `work/task-<id>-<description>` |
| Integration | `integration/<feature>` |

## Files

```
parallel-orchestrator/SKILL.md    # Main orchestration skill (584 lines)
parallel-worker/SKILL.md          # Worker execution skill (424 lines)
parallel-retrospective/SKILL.md   # Analysis skill (569 lines)
```

## Design Decisions

**Git as communication layer**: Workers never communicate directly. All coordination happens through git commits. This makes the system simple, auditable, and works with any number of workers.

**Human in the loop**: The orchestrator outputs commands and prompts; you execute them. You spawn worker sessions manually. This keeps you in control and aware of what's happening.

**Phase 0 input analysis**: The orchestrator can parse markdown documents (code reviews, issue lists) and extract work items, estimate complexity, and assess parallelizability before proposing a split.

## Limitations

- Requires manual session spawning (no automated agent spawning)
- Works best with clearly separable tasks
- Merge conflicts still require manual resolution
- Git overhead may not be worth it for small tasks (<3 items, <2 hours work)

## License

MIT
