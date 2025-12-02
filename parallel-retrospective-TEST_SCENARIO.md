# Test Scenario: Parallel Retrospective Skill

This document validates the `parallel-retrospective` skill by demonstrating a complete retrospective analysis of a mixed-results workflow.

## Hypothetical Workflow

**Feature**: User notification system for a Python Django web application

**Workers**:
- task-1-notification-models: Database models and migrations
- task-2-notification-api: REST API endpoints
- task-3-notification-frontend: Frontend templates and JavaScript

**Outcomes**:
- Worker 1 (models): Completed smoothly
- Worker 2 (API): Hit `[BLOCKED]` state, eventually resolved
- Worker 3 (frontend): Had `[NEEDS:...]` cross-dependency on API
- Integration: One merge conflict
- Incident: Worker 2 accidentally committed to main once (detected and recovered)

---

## Simulated Git History

### Worker 1: task-1-notification-models (Smooth completion)

```
abc1001 2024-01-15 09:15 [CHECKPOINT] Add Notification model with fields
abc1002 2024-01-15 09:45 [CHECKPOINT] Add NotificationPreference model
abc1003 2024-01-15 10:30 [CHECKPOINT] Generate Django migrations
abc1004 2024-01-15 11:00 [COMPLETE] Notification models ready for use

Summary of changes:
- Notification model (user, type, message, read_status, created_at)
- NotificationPreference model (user, channel, enabled)
- Migration 0015_add_notifications

Files changed:
- models/notification.py
- models/__init__.py
- migrations/0015_add_notifications.py
- tests/test_notification_models.py
```

### Worker 2: task-2-notification-api (Blocked, then recovered)

```
def2001 2024-01-15 09:20 [CHECKPOINT] Create notification API blueprint
def2002 2024-01-15 10:00 [CHECKPOINT] Add list notifications endpoint
def2003 2024-01-15 11:30 [BLOCKED:database-migration-pending] Cannot test API without models migrated

The notification models exist but migrations haven't been run on the dev database.
Completed:
- GET /api/notifications endpoint structure
- POST /api/notifications/read endpoint structure

Blocked on:
- All endpoints requiring database queries

def2004 2024-01-15 13:00 [CHECKPOINT] Resume after migration - endpoints working
def2005 2024-01-15 14:00 [CHECKPOINT] Add push notification trigger endpoint
def2006 2024-01-15 15:00 [COMPLETE] Notification API fully implemented

Summary of changes:
- GET /api/notifications - list user notifications
- POST /api/notifications/read - mark as read
- POST /api/notifications/send - trigger notification

Files changed:
- api/notifications/__init__.py
- api/notifications/routes.py
- api/notifications/serializers.py
- tests/test_notification_api.py
```

### Worker 3: task-3-notification-frontend (Cross-dependency)

```
ghi3001 2024-01-15 09:30 [CHECKPOINT] Create notification dropdown component
ghi3002 2024-01-15 10:30 [CHECKPOINT] Add notification badge with count
ghi3003 2024-01-15 11:45 [NEEDS:task-2/api-endpoints] Cannot complete real-time updates

Need the API endpoints from task-2 to implement:
- Polling for new notifications
- Mark-as-read functionality

Can continue with:
- Static UI components
- CSS styling

ghi3004 2024-01-15 12:30 [CHECKPOINT] Complete notification styling
ghi3005 2024-01-15 14:30 [CHECKPOINT] Integrate with API after task-2 complete
ghi3006 2024-01-15 15:30 [COMPLETE] Notification frontend implemented

Summary of changes:
- Notification dropdown component
- Real-time badge updates
- Mark-as-read UI
- Responsive styling

Files changed:
- templates/components/notification_dropdown.html
- static/js/notifications.js
- static/css/notifications.css
```

### Wrong-Branch Incident (Worker 2)

```
# Accidentally committed to main at 10:15
main: xyz9999 2024-01-15 10:15 [CHECKPOINT] Add notification serializers

# Detected by orchestrator at 10:20
# Recovery performed:
git checkout main
git reset --hard origin/main^
git checkout work/task-2-notification-api
git cherry-pick xyz9999
# Resulted in commit def2002b
```

### Integration Branch

```
int4001 2024-01-15 16:00 Merge task-1: Notification models
int4002 2024-01-15 16:10 Merge task-2: Notification API
int4003 2024-01-15 16:12 Resolve merge conflict: duplicate import in models/__init__.py
int4004 2024-01-15 16:20 Merge task-3: Notification frontend
int4005 2024-01-15 16:30 Final integration verified
```

---

## Retrospective Analysis

### Data Gathered

```bash
# Worker branches found
origin/work/task-1-notification-models
origin/work/task-2-notification-api
origin/work/task-3-notification-frontend

# Commits per worker
task-1: 4 commits
task-2: 6 commits
task-3: 6 commits

# Prefix usage
[CHECKPOINT]: 10 occurrences
[BLOCKED:...]: 1 occurrence
[NEEDS:...]: 1 occurrence
[COMPLETE]: 3 occurrences

# Integration
Merge commits: 3
Conflict resolution commits: 1
```

### Timeline Reconstruction

| Time | Worker | Event |
|------|--------|-------|
| 09:15 | task-1 | Started - first checkpoint |
| 09:20 | task-2 | Started - first checkpoint |
| 09:30 | task-3 | Started - first checkpoint |
| 10:00 | task-2 | Progress checkpoint |
| 10:15 | task-2 | **INCIDENT**: Committed to main |
| 10:20 | - | Recovery: cherry-picked to correct branch |
| 10:30 | task-1 | Progress checkpoint |
| 10:30 | task-3 | Progress checkpoint |
| 11:00 | task-1 | **COMPLETE** (first to finish) |
| 11:30 | task-2 | **BLOCKED**: database migration pending |
| 11:45 | task-3 | **NEEDS**: waiting for task-2 API |
| 12:30 | task-3 | Continued with styling (worked around block) |
| 13:00 | task-2 | Resumed after block resolved |
| 14:00 | task-2 | Progress checkpoint |
| 14:30 | task-3 | Integrated with API |
| 15:00 | task-2 | **COMPLETE** |
| 15:30 | task-3 | **COMPLETE** |
| 16:00-16:30 | - | Integration phase |

---

## Example Retrospective Report

```markdown
# Retrospective Report: notification-system

**Date**: 2024-01-15
**Workflow Duration**: 09:15 to 16:30 (7.25 hours)
**Mode**: Post-completion

---

## Summary

- **Grade**: B
- **Assessment**: The workflow completed successfully with all features delivered. However, several issues impacted efficiency: a blocking condition caused a 1.5-hour delay for the API worker, a cross-dependency blocked frontend work for nearly 3 hours, and one wrong-branch commit required recovery. Despite these issues, workers used conventions correctly, the problems were detected and resolved, and integration completed with only one minor merge conflict.
- **Key Metrics**:
  | Metric | Value |
  |--------|-------|
  | Workers | 3 |
  | Total commits | 16 |
  | Checkpoints | 10 |
  | Blocks encountered | 1 |
  | Cross-dependencies | 1 |
  | Merge conflicts | 1 |

---

## What Worked Well

### Consistent Convention Usage
All three workers used commit prefixes correctly throughout the workflow. Status was always parseable from commit messages.

**Evidence**: All 16 commits followed the `[PREFIX] description` format.

### Effective Block Signaling
When Worker 2 encountered a database issue, they properly signaled with `[BLOCKED:database-migration-pending]` and documented what was completed vs. blocked.

**Evidence**: commit `def2003` - "[BLOCKED:database-migration-pending] Cannot test API without models migrated"

### Workaround Behavior
Worker 3 correctly continued with non-blocked work (styling) while waiting for the API dependency.

**Evidence**: commit `ghi3004` - "[CHECKPOINT] Complete notification styling" (made during wait)

### Quick Wrong-Branch Recovery
The accidental commit to main was detected within 5 minutes and recovered without data loss.

**Evidence**: Incident at 10:15, recovery complete by 10:20.

---

## Problems Identified

| # | Category | Severity | Description | Evidence |
|---|----------|----------|-------------|----------|
| 1 | Execution | Moderate | Wrong-branch commit | commit `xyz9999` on main |
| 2 | Planning | Moderate | Predictable cross-dependency not addressed | commit `ghi3003` |
| 3 | Execution | Minor | BLOCKED due to environment issue | commit `def2003` |
| 4 | Integration | Minor | Merge conflict from shared file | commit `int4003` |

### Problem 1: Wrong-Branch Commit

**Category**: Execution
**Severity**: Moderate

**Description**: Worker 2 committed directly to main instead of their worktree branch. This violates the core safety principle of the worker skill.

**Evidence**:
- commit `xyz9999` on main: "[CHECKPOINT] Add notification serializers"
- Recovery required manual intervention

**Impact**: 5 minutes of orchestrator time for recovery. Risk of polluting main branch.

### Problem 2: Predictable Cross-Dependency

**Category**: Planning
**Severity**: Moderate

**Description**: Worker 3 (frontend) was always going to need Worker 2's (API) endpoints. This dependency was predictable at planning time but wasn't structured to minimize wait time.

**Evidence**:
- commit `ghi3003`: "[NEEDS:task-2/api-endpoints] Cannot complete real-time updates"
- Frontend waited ~2.75 hours (11:45 to 14:30) for API

**Impact**: Suboptimal parallelism. Frontend could have been sequenced after API.

### Problem 3: Environment-Related Block

**Category**: Execution
**Severity**: Minor

**Description**: Worker 2 was blocked because the database migrations hadn't been applied to dev environment. This is an environmental issue outside the workflow itself.

**Evidence**:
- commit `def2003`: "[BLOCKED:database-migration-pending]"
- Block lasted 1.5 hours (11:30 to 13:00)

**Impact**: Delay in API completion, cascading delay to frontend.

### Problem 4: Shared File Conflict

**Category**: Integration
**Severity**: Minor

**Description**: Both Worker 1 (models) and Worker 2 (API) modified `models/__init__.py`, causing a merge conflict.

**Evidence**:
- commit `int4003`: "Resolve merge conflict: duplicate import in models/__init__.py"

**Impact**: Minor—conflict was trivial to resolve (duplicate imports).

---

## Root Cause Analysis

| Problem | Root Cause Type | Explanation |
|---------|-----------------|-------------|
| Wrong-branch commit | Worker error | Worker didn't verify branch before committing, despite skill instructions |
| Predictable dependency | Orchestrator decision | Orchestrator could have identified frontend→API dependency and adjusted timing |
| Environment block | Unavoidable complexity | External environment issue, not workflow design |
| Shared file conflict | Orchestrator decision | Could have assigned `__init__.py` to single worker or used merge strategy |

---

## Recommendations

### Skill Updates

**parallel-worker**:
- Add a pre-commit hook reminder or checklist that MUST be followed
- Consider adding a "branch verification" step that outputs current branch before every commit command

**parallel-orchestrator**:
- During planning phase, add explicit check for "files modified by multiple workers" and flag for conflict risk
- When known dependencies exist (frontend needs API), consider suggesting staggered starts or explicit handoff points

### Process Improvements

- **Pre-workflow environment check**: Before starting parallel work, verify all workers have necessary environment setup (migrations, dependencies)
- **Dependency-aware scheduling**: When frontend depends on API, consider starting frontend 1-2 hours later, or having explicit handoff checkpoint

### Tooling Gaps

- **Automated branch verification**: A git hook that prevents commits to main from worktree directories would have caught Problem 1
- **Conflict prediction**: Orchestrator could analyze planned scope and warn about files likely to conflict

---

## Proposed Skill Patches

### Patch 1: Strengthen Branch Verification in Worker Skill

**Target**: `~/.claude/skills/parallel-worker/SKILL.md`
**Section**: Commit Workflow
**Rationale**: Worker 2's wrong-branch commit suggests the current guidance isn't prominent enough.

**Add after "### Before EVERY Commit Sequence" section**:
```markdown
### Branch Safety Script

Run this BEFORE your first commit of any session:

```bash
# Create alias for safe commits
alias safe-commit='git branch --show-current | grep -q "^work/task-" && git commit || echo "ERROR: Not on worker branch!"'
```

If you're using this alias and see "ERROR: Not on worker branch!", STOP and fix your branch before proceeding.
```

### Patch 2: Add Conflict Risk Assessment to Orchestrator

**Target**: `~/.claude/skills/parallel-orchestrator/SKILL.md`
**Section**: Phase 1: Planning
**Rationale**: The merge conflict was predictable—both workers needed to touch `__init__.py`.

**Add after "### Identify Cross-Dependencies"**:
```markdown
### Assess Merge Conflict Risk

Identify files likely to be modified by multiple workers:

**High-risk shared files**:
- `__init__.py` files in shared packages
- Configuration files (`settings.py`, `config.py`)
- Route registrations
- Import aggregations

**Mitigation options**:
1. Assign shared file to ONE worker; others add TODO comments
2. Have orchestrator make shared file changes at integration
3. Accept conflict risk but document expected conflicts upfront
```

### Patch 3: Add Dependency Scheduling Guidance

**Target**: `~/.claude/skills/parallel-orchestrator/SKILL.md`
**Section**: Phase 1: Planning
**Rationale**: Frontend waited nearly 3 hours for API—scheduling could have minimized this.

**Add after workstream split template**:
```markdown
### Dependency-Aware Scheduling

When Worker B depends on Worker A's output:

| Scenario | Recommendation |
|----------|----------------|
| A is fast, B can start in parallel | Start together; B works on non-dependent parts first |
| A is slow, B mostly depends on A | Start B 1-2 hours after A; include explicit handoff checkpoint |
| Tight coupling | Consider making it one worker's scope |

**Explicit Handoff Example**:
"Worker A: Commit `[CHECKPOINT] API contract ready` when endpoints are defined. Worker B: Wait for this checkpoint before API integration work."
```

---

## Appendix: Full Commit Timeline

| Time | Worker | Commit | Message |
|------|--------|--------|---------|
| 09:15 | task-1 | abc1001 | [CHECKPOINT] Add Notification model with fields |
| 09:20 | task-2 | def2001 | [CHECKPOINT] Create notification API blueprint |
| 09:30 | task-3 | ghi3001 | [CHECKPOINT] Create notification dropdown component |
| 09:45 | task-1 | abc1002 | [CHECKPOINT] Add NotificationPreference model |
| 10:00 | task-2 | def2002 | [CHECKPOINT] Add list notifications endpoint |
| 10:15 | task-2 | xyz9999 | **ON MAIN** [CHECKPOINT] Add notification serializers |
| 10:20 | - | - | Recovery: cherry-picked to task-2 branch |
| 10:30 | task-1 | abc1003 | [CHECKPOINT] Generate Django migrations |
| 10:30 | task-3 | ghi3002 | [CHECKPOINT] Add notification badge with count |
| 11:00 | task-1 | abc1004 | [COMPLETE] Notification models ready for use |
| 11:30 | task-2 | def2003 | [BLOCKED:database-migration-pending] |
| 11:45 | task-3 | ghi3003 | [NEEDS:task-2/api-endpoints] |
| 12:30 | task-3 | ghi3004 | [CHECKPOINT] Complete notification styling |
| 13:00 | task-2 | def2004 | [CHECKPOINT] Resume after migration |
| 14:00 | task-2 | def2005 | [CHECKPOINT] Add push notification trigger |
| 14:30 | task-3 | ghi3005 | [CHECKPOINT] Integrate with API |
| 15:00 | task-2 | def2006 | [COMPLETE] Notification API fully implemented |
| 15:30 | task-3 | ghi3006 | [COMPLETE] Notification frontend implemented |
| 16:00 | - | int4001 | Merge task-1: Notification models |
| 16:10 | - | int4002 | Merge task-2: Notification API |
| 16:12 | - | int4003 | Resolve merge conflict |
| 16:20 | - | int4004 | Merge task-3: Notification frontend |
| 16:30 | - | int4005 | Final integration verified |
```

---

## Validation Checklist

This test scenario validates:

1. **Convention recognition** - Retrospective correctly parses all four prefixes:
   - `[CHECKPOINT]` - 10 instances analyzed
   - `[BLOCKED:<reason>]` - 1 instance with reason extraction
   - `[NEEDS:task-X/<item>]` - 1 instance with dependency identification
   - `[COMPLETE]` - 3 instances verified

2. **Problem categorization** - Four categories demonstrated:
   - Planning (predictable dependency)
   - Execution (wrong branch, environment block)
   - Integration (merge conflict)

3. **Root cause types** - All four types demonstrated:
   - Skill design flaw (addressed in patches)
   - Orchestrator decision (dependency scheduling)
   - Worker error (wrong branch)
   - Unavoidable complexity (environment issue)

4. **Grading system** - Grade B justified with:
   - All workers completed (meets completion criteria)
   - Issues present but resolved (not grade A)
   - No failures or abandoned work (not grade D/F)

5. **Skill patches** - Three concrete patches proposed:
   - Worker: Branch safety script
   - Orchestrator: Conflict risk assessment
   - Orchestrator: Dependency scheduling

6. **Evidence-based analysis** - Every finding cites specific commits
