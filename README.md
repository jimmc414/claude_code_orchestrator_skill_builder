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

See [INSTALL.md](INSTALL.md) for installation. See [CHEATSHEET.md](CHEATSHEET.md) for quick reference.

## Usage

### Workflow Order

```
1. ORCHESTRATOR  -->  2. WORKERS (parallel)  -->  3. ORCHESTRATOR  -->  4. RETROSPECTIVE
   (analyze/setup)       (execute tasks)           (monitor/integrate)    (optional)
```

### Step 1: Start the Orchestrator

**From documents:**
```
Use the parallel-orchestrator skill to analyze these documents and propose
a parallel workflow:

@code_review.md
@missing_tests.md
```

**From direct instructions:**
```
Use the parallel-orchestrator skill to split this task into parallel workstreams:

Add user authentication with:
- Database models for users and sessions
- REST API endpoints for login/logout/register
- Frontend login form and session management
```

### Step 2: Launch Workers

Copy each kickoff prompt the orchestrator generates into a new Claude Code session. Workers execute independently.

### Step 3: Monitor Progress

```
Check on the parallel workers and show me their status.
```

### Step 4: Integrate

```
All workers are complete. Integrate the changes and prepare for merge to main.
```

### Step 5: Retrospective (Optional)

```
Use the parallel-retrospective skill to analyze the completed workflow.

Feature: user-authentication
Integration branch: integration/user-auth
Worker branches: work/task-1-models, work/task-2-api, work/task-3-frontend

What worked well? What should we improve for next time?
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
