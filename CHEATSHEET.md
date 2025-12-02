# Skills Cheat Sheet

## Workflow Order

```
1. ORCHESTRATOR (Phase 0 + Planning + Setup)
2. WORKERS (parallel execution)
3. ORCHESTRATOR (Monitoring + Integration)
4. RETROSPECTIVE (optional, post-completion)
```

## Invoking the Orchestrator

### From Documents (Code Review, Issue List)

```
Use the parallel-orchestrator skill to analyze these documents and propose
a parallel workflow:

@code_review.md
@missing_tests.md
```

### From Direct Instructions

```
Use the parallel-orchestrator skill to split this task into parallel workstreams:

Add user authentication with:
- Database models for users and sessions
- REST API endpoints for login/logout/register
- Frontend login form and session management
```

### Check Worker Progress

```
Check on the parallel workers and show me their status.
```

### Integrate Completed Work

```
All workers are complete. Integrate the changes and prepare for merge to main.
```

## Invoking Workers

Workers are invoked by copying the orchestrator-generated kickoff prompt into a new Claude Code session. The kickoff prompt contains everything the worker needs.

### If You Need to Restart a Worker

```
Use the parallel-worker skill to continue work on task-2-api-endpoints.

The previous session crashed. Here's the original assignment:
[paste kickoff prompt]

Check the branch for completed work and continue from the last checkpoint.
```

## Invoking the Retrospective

### After Successful Completion

```
Use the parallel-retrospective skill to analyze the completed workflow.

Feature: user-authentication
Integration branch: integration/user-auth
Worker branches: work/task-1-models, work/task-2-api, work/task-3-frontend

What worked well? What should we improve for next time?
```

### After Failure or Abandonment

```
Use the parallel-retrospective skill to analyze why this workflow failed.

Feature: payment-integration
Worker branches: work/task-1-models, work/task-2-api
Failure point: task-2 stalled after 3 hours with no commits

What went wrong and how do we prevent it next time?
```

### With Planning Quality Assessment

```
Use the parallel-retrospective skill to analyze this workflow.
Include planning quality assessment using the original work item manifest.

[paste Work Item Manifest from Phase 0]

Evaluate whether the complexity estimates and grouping decisions were accurate.
```

## Quick Reference: Commit Prefixes

| Prefix | Meaning | Example |
|--------|---------|---------|
| `[CHECKPOINT]` | Subtask done, continuing | `[CHECKPOINT] Add user model` |
| `[BLOCKED:reason]` | Cannot proceed | `[BLOCKED:missing-api] Need endpoints` |
| `[NEEDS:task-X/item]` | Need other worker's output | `[NEEDS:task-1/UserModel]` |
| `[COMPLETE]` | All tasks finished | `[COMPLETE] API endpoints done` |

## Quick Reference: Naming

| Item | Pattern | Example |
|------|---------|---------|
| Worktree | `worktrees/task-<id>-<desc>/` | `worktrees/task-1-models/` |
| Branch | `work/task-<id>-<desc>` | `work/task-1-models` |
| Integration | `integration/<feature>` | `integration/user-auth` |
