# Test Scenario: Parallel Workflow Skills

This document validates the `parallel-orchestrator` and `parallel-worker` skills by walking through a complete example with 3 workers.

## Hypothetical Task

**Feature Request:** Add a user analytics dashboard to an existing Python Flask web application.

**Repository Structure:**
```
myapp/
├── api/
│   ├── __init__.py
│   ├── auth.py
│   └── users.py
├── models/
│   ├── __init__.py
│   └── user.py
├── migrations/
│   └── versions/
├── templates/
│   └── base.html
├── static/
│   ├── css/
│   └── js/
├── tests/
│   ├── test_api.py
│   └── test_models.py
└── config.py
```

---

## Phase 1: Orchestrator Planning

### Workstream Split Proposal

```markdown
## Proposed Workstream Split

### Worker 1: task-1-api-endpoints
- **Scope**: `api/analytics/`, `tests/test_api_analytics.py`
- **Tasks**:
  1. Create analytics API module structure
  2. Implement GET /api/analytics/summary endpoint
  3. Implement GET /api/analytics/events endpoint
  4. Implement POST /api/analytics/track endpoint
  5. Add API tests
- **Dependencies**: Needs Analytics model from task-2
- **Checkpoints**: Commit after each endpoint

### Worker 2: task-2-models-migrations
- **Scope**: `models/analytics.py`, `migrations/versions/`, `tests/test_models_analytics.py`
- **Tasks**:
  1. Create AnalyticsEvent model
  2. Create AnalyticsSummary model
  3. Generate Alembic migration
  4. Add model tests
- **Dependencies**: None
- **Checkpoints**: Commit after each model, then after migration

### Worker 3: task-3-frontend
- **Scope**: `templates/analytics/`, `static/js/analytics/`, `static/css/analytics/`
- **Tasks**:
  1. Create dashboard template
  2. Implement chart components (JS)
  3. Add real-time update functionality
  4. Style dashboard (CSS)
- **Dependencies**: Needs API endpoints from task-1 for integration testing
- **Checkpoints**: Commit after template, charts, real-time, styling

### Cross-Dependencies Detected
- Worker 1 (API) needs models from Worker 2
- Worker 3 (Frontend) needs API from Worker 1
- Suggested order: Worker 2 → Worker 1 → Worker 3 (can work in parallel on non-dependent parts)
```

---

## Phase 2: Orchestrator Setup

### Generated Setup Commands

```bash
# Create worktrees directory
mkdir -p worktrees

# Create worktree for each worker
git worktree add worktrees/task-1-api-endpoints -b work/task-1-api-endpoints
git worktree add worktrees/task-2-models-migrations -b work/task-2-models-migrations
git worktree add worktrees/task-3-frontend -b work/task-3-frontend

# Verify worktrees
git worktree list
```

**Expected Output:**
```
/home/user/myapp                         abc1234 [main]
/home/user/myapp/worktrees/task-1-api-endpoints    abc1234 [work/task-1-api-endpoints]
/home/user/myapp/worktrees/task-2-models-migrations abc1234 [work/task-2-models-migrations]
/home/user/myapp/worktrees/task-3-frontend         abc1234 [work/task-3-frontend]
```

### Generated Kickoff Prompts

#### Kickoff Prompt: Worker 1

```markdown
# Worker Kickoff: task-1-api-endpoints

## Assignment
You are Worker 1 in a parallel workflow. Work ONLY within your assigned scope.

## Your Worktree
```bash
cd worktrees/task-1-api-endpoints
```

## Assigned Scope
You may ONLY modify files in:
- `api/analytics/` (create this directory)
- `tests/test_api_analytics.py` (create this file)

Do NOT modify any other files.

## Tasks
1. Create `api/analytics/__init__.py` with blueprint setup
2. Implement GET `/api/analytics/summary` - returns aggregated analytics
3. Implement GET `/api/analytics/events` - returns recent events
4. Implement POST `/api/analytics/track` - records new event
5. Add comprehensive API tests

## Checkpoint Expectations
Commit after completing:
- Blueprint structure setup
- Each endpoint implementation
- Test suite

## Commit Protocol
1. ALWAYS verify branch before committing: `git branch`
2. Use these prefixes:
   - `[CHECKPOINT]` - subtask complete, continuing
   - `[BLOCKED:<reason>]` - cannot proceed
   - `[NEEDS:task-X/<item>]` - need output from another worker
   - `[COMPLETE]` - all tasks finished

## Dependencies
You need the Analytics models from Worker 2 (task-2-models-migrations).
- If models not available, commit `[NEEDS:task-2/AnalyticsEvent]` and continue with mock data
- You can stub the model imports initially

## Start
1. Verify you're on the correct branch: `git branch`
2. Create the api/analytics/ directory structure
3. Begin implementing endpoints
```

#### Kickoff Prompt: Worker 2

```markdown
# Worker Kickoff: task-2-models-migrations

## Assignment
You are Worker 2 in a parallel workflow. Work ONLY within your assigned scope.

## Your Worktree
```bash
cd worktrees/task-2-models-migrations
```

## Assigned Scope
You may ONLY modify files in:
- `models/analytics.py` (create this file)
- `models/__init__.py` (add imports only)
- `migrations/versions/` (generated migrations)
- `tests/test_models_analytics.py` (create this file)

## Tasks
1. Create AnalyticsEvent model (user_id, event_type, timestamp, metadata)
2. Create AnalyticsSummary model (user_id, date, event_counts, computed_metrics)
3. Generate Alembic migration for new models
4. Add model unit tests

## Checkpoint Expectations
Commit after completing:
- AnalyticsEvent model
- AnalyticsSummary model
- Migration generation
- Test suite

## Commit Protocol
1. ALWAYS verify branch before committing: `git branch`
2. Use these prefixes:
   - `[CHECKPOINT]` - subtask complete, continuing
   - `[BLOCKED:<reason>]` - cannot proceed
   - `[NEEDS:task-X/<item>]` - need output from another worker
   - `[COMPLETE]` - all tasks finished

## Dependencies
None - you have no dependencies on other workers.

## Start
1. Verify you're on the correct branch: `git branch`
2. Create models/analytics.py
3. Begin with AnalyticsEvent model
```

#### Kickoff Prompt: Worker 3

```markdown
# Worker Kickoff: task-3-frontend

## Assignment
You are Worker 3 in a parallel workflow. Work ONLY within your assigned scope.

## Your Worktree
```bash
cd worktrees/task-3-frontend
```

## Assigned Scope
You may ONLY modify files in:
- `templates/analytics/` (create this directory)
- `static/js/analytics/` (create this directory)
- `static/css/analytics/` (create this directory)

Do NOT modify any other files.

## Tasks
1. Create `templates/analytics/dashboard.html` extending base.html
2. Implement `static/js/analytics/charts.js` - Chart.js visualizations
3. Implement `static/js/analytics/realtime.js` - WebSocket updates
4. Create `static/css/analytics/dashboard.css` - dashboard styling

## Checkpoint Expectations
Commit after completing:
- Dashboard template structure
- Chart components
- Real-time functionality
- Styling complete

## Commit Protocol
1. ALWAYS verify branch before committing: `git branch`
2. Use these prefixes:
   - `[CHECKPOINT]` - subtask complete, continuing
   - `[BLOCKED:<reason>]` - cannot proceed
   - `[NEEDS:task-X/<item>]` - need output from another worker
   - `[COMPLETE]` - all tasks finished

## Dependencies
You need API endpoints from Worker 1 for testing the frontend integration.
- Build frontend assuming API contract: GET /api/analytics/summary, GET /api/analytics/events
- If you need to test against real API, commit `[NEEDS:task-1/api-endpoints]`
- You can use mock data for development

## Start
1. Verify you're on the correct branch: `git branch`
2. Create the directory structure
3. Begin with dashboard template
```

---

## Phase 3: Worker Execution Examples

### Worker 2 (Models) - Normal Flow

```bash
# Worker 2 session
cd worktrees/task-2-models-migrations
git branch
# * work/task-2-models-migrations

# Create AnalyticsEvent model
# ... coding ...

git add models/analytics.py
git commit -m "[CHECKPOINT] Add AnalyticsEvent model

Implemented AnalyticsEvent with fields:
- id: Primary key
- user_id: Foreign key to User
- event_type: String enum (page_view, click, conversion)
- timestamp: DateTime with timezone
- metadata: JSON field for flexible data

Files changed:
- models/analytics.py"

git push origin work/task-2-models-migrations

# Create AnalyticsSummary model
# ... coding ...

git add models/analytics.py
git commit -m "[CHECKPOINT] Add AnalyticsSummary model

Implemented AnalyticsSummary with fields:
- id: Primary key
- user_id: Foreign key to User
- date: Date field
- total_events: Integer
- events_by_type: JSON counts
- computed_metrics: JSON aggregations

Files changed:
- models/analytics.py"

git push origin work/task-2-models-migrations

# Generate migration
# ... alembic revision --autogenerate ...

git add migrations/versions/
git commit -m "[CHECKPOINT] Generate analytics migration

Created migration: 2024_01_15_add_analytics_tables

Changes:
- Creates analytics_event table
- Creates analytics_summary table
- Adds indexes for user_id and timestamp lookups

Files changed:
- migrations/versions/2024_01_15_add_analytics_tables.py"

git push origin work/task-2-models-migrations

# Add tests
# ... coding ...

git add tests/test_models_analytics.py models/__init__.py
git commit -m "[COMPLETE] Analytics models with tests

Summary of changes:
- AnalyticsEvent model with validation
- AnalyticsSummary model with aggregation methods
- Alembic migration for both tables
- Comprehensive test suite (15 tests, all passing)

Files changed:
- models/analytics.py
- models/__init__.py
- migrations/versions/2024_01_15_add_analytics_tables.py
- tests/test_models_analytics.py

Integration notes:
- Models are ready for use by API layer
- Import with: from models.analytics import AnalyticsEvent, AnalyticsSummary"

git push origin work/task-2-models-migrations
```

### Worker 1 (API) - Cross-Dependency Flow

```bash
# Worker 1 session
cd worktrees/task-1-api-endpoints
git branch
# * work/task-1-api-endpoints

# Create blueprint structure
# ... coding ...

git add api/analytics/
git commit -m "[CHECKPOINT] Create analytics API blueprint structure

Set up:
- api/analytics/__init__.py with Flask blueprint
- api/analytics/routes.py for endpoint definitions
- Stub imports for models (not yet available)

Files changed:
- api/analytics/__init__.py
- api/analytics/routes.py"

git push origin work/task-1-api-endpoints

# Implement summary endpoint - realizes model needed
# ... tries to import AnalyticsEvent ...

git add api/analytics/routes.py
git commit -m "[NEEDS:task-2/AnalyticsEvent] Summary endpoint needs Analytics model

Started implementing GET /api/analytics/summary but need AnalyticsEvent model
for database queries.

Completed:
- Endpoint route definition
- Request parameter parsing
- Response schema

Blocked on:
- Actual database queries (need model)

Continuing with mock implementation for other endpoints..."

git push origin work/task-1-api-endpoints

# Continue with events endpoint using mocks
# ... coding with mock data ...

git add api/analytics/routes.py
git commit -m "[CHECKPOINT] Add events endpoint with mock data

Implemented GET /api/analytics/events:
- Pagination support
- Date range filtering
- Returns mock data until models available

Files changed:
- api/analytics/routes.py"

git push origin work/task-1-api-endpoints

# Later: orchestrator notifies that task-2 is complete
# Pull task-2 changes

git fetch origin work/task-2-models-migrations
git merge origin/work/task-2-models-migrations -m "Merge task-2 models for API integration"

# Now complete with real models
# ... finish implementation ...

git add api/analytics/ tests/test_api_analytics.py
git commit -m "[COMPLETE] Analytics API endpoints implemented

Summary of changes:
- GET /api/analytics/summary - aggregated metrics by date range
- GET /api/analytics/events - paginated event list with filters
- POST /api/analytics/track - record new analytics event
- Full test coverage (22 tests)

Files changed:
- api/analytics/__init__.py
- api/analytics/routes.py
- tests/test_api_analytics.py

Integration notes:
- Requires models from task-2 (merged)
- Register blueprint in app factory with: app.register_blueprint(analytics_bp, url_prefix='/api/analytics')"

git push origin work/task-1-api-endpoints
```

### Worker 3 (Frontend) - Blocked Scenario

```bash
# Worker 3 session
cd worktrees/task-3-frontend
git branch
# * work/task-3-frontend

# Create template
# ... coding ...

git add templates/analytics/
git commit -m "[CHECKPOINT] Create analytics dashboard template

Implemented dashboard.html:
- Extends base.html
- Grid layout for chart widgets
- Placeholder areas for real-time updates
- Navigation integration

Files changed:
- templates/analytics/dashboard.html
- templates/analytics/partials/chart_widget.html"

git push origin work/task-3-frontend

# Create chart components
# ... coding ...

git add static/js/analytics/charts.js
git commit -m "[CHECKPOINT] Implement chart components

Added charts.js with:
- SummaryChart class for overview metrics
- EventsTimeline class for time series
- Chart.js integration with custom theme

Files changed:
- static/js/analytics/charts.js"

git push origin work/task-3-frontend

# Start real-time - needs API to test
# Can't proceed without knowing exact API response format

git add static/js/analytics/realtime.js
git commit -m "[BLOCKED:api-response-format] Cannot complete real-time updates

Started realtime.js but need to know exact API response format for:
- GET /api/analytics/summary response schema
- WebSocket event message format

Completed:
- WebSocket connection handling
- Base event listener structure

Cannot complete:
- Data parsing (unknown schema)
- Chart update logic (depends on data structure)

All frontend work is blocked until API is available for testing."

git push origin work/task-3-frontend

# ... wait for orchestrator guidance ...

# Later: orchestrator provides API schema from task-1

git add static/js/analytics/ static/css/analytics/
git commit -m "[COMPLETE] Analytics frontend implemented

Summary of changes:
- Dashboard template with responsive grid
- Chart.js visualizations for all metrics
- Real-time WebSocket updates
- Complete CSS styling

Files changed:
- templates/analytics/dashboard.html
- templates/analytics/partials/chart_widget.html
- static/js/analytics/charts.js
- static/js/analytics/realtime.js
- static/css/analytics/dashboard.css

Integration notes:
- Expects API at /api/analytics/* endpoints
- WebSocket expects connection at /ws/analytics
- Include CSS before base.css, JS after Chart.js CDN"

git push origin work/task-3-frontend
```

---

## Phase 4: Orchestrator Monitoring

### Status Check Commands

```bash
# Fetch all branches
git fetch --all

# Check commits on all worker branches
for branch in work/task-1-api-endpoints work/task-2-models-migrations work/task-3-frontend; do
  echo "=== origin/$branch ==="
  git log origin/$branch --oneline -5
done
```

### Status Report Output

```markdown
## Worker Status Report - 2:30 PM

| Worker | Branch | Last Commit | Age | Status |
|--------|--------|-------------|-----|--------|
| task-1 | work/task-1-api-endpoints | 45m ago | 45m | [NEEDS:task-2/AnalyticsEvent] |
| task-2 | work/task-2-models-migrations | 20m ago | 20m | [COMPLETE] |
| task-3 | work/task-3-frontend | 10m ago | 10m | [BLOCKED:api-response-format] |

### Commit Log Summary

**task-1-api-endpoints (4 commits):**
- `[CHECKPOINT]` Create analytics API blueprint structure
- `[NEEDS:task-2/AnalyticsEvent]` Summary endpoint needs Analytics model
- `[CHECKPOINT]` Add events endpoint with mock data
- *(waiting for task-2)*

**task-2-models-migrations (4 commits):**
- `[CHECKPOINT]` Add AnalyticsEvent model
- `[CHECKPOINT]` Add AnalyticsSummary model
- `[CHECKPOINT]` Generate analytics migration
- `[COMPLETE]` Analytics models with tests

**task-3-frontend (3 commits):**
- `[CHECKPOINT]` Create analytics dashboard template
- `[CHECKPOINT]` Implement chart components
- `[BLOCKED:api-response-format]` Cannot complete real-time updates

### Detected Issues

1. **Cross-dependency resolved:**
   - task-1 needed `task-2/AnalyticsEvent`
   - task-2 is now [COMPLETE]
   - Action: Notify task-1 to merge task-2 and continue

2. **Worker blocked:**
   - task-3 is [BLOCKED:api-response-format]
   - Needs API response schema from task-1
   - Action: Once task-1 completes, share API contract with task-3

### Recommended Actions

1. Send resume prompt to Worker 1:
   ```
   task-2 (models) is complete. Merge and continue:
   git fetch origin work/task-2-models-migrations
   git merge origin/work/task-2-models-migrations
   ```

2. Wait for task-1 to complete, then unblock task-3 with API schema
```

---

## Phase 5: Integration

### Integration Sequence

```bash
# Create integration branch
git checkout main
git pull origin main
git checkout -b integration/analytics-dashboard

# Merge task-2 first (no dependencies)
git merge origin/work/task-2-models-migrations --no-ff -m "Merge task-2: Analytics models and migrations"
# Success - clean merge

# Merge task-1 (depends on task-2)
git merge origin/work/task-1-api-endpoints --no-ff -m "Merge task-1: Analytics API endpoints"
```

### Merge Conflict Example

```
Auto-merging models/__init__.py
CONFLICT (content): Merge conflict in models/__init__.py
Automatic merge failed; fix conflicts and then commit the result.
```

**Conflict in `models/__init__.py`:**
```python
<<<<<<< HEAD
from models.user import User
from models.analytics import AnalyticsEvent, AnalyticsSummary
=======
from models.user import User
from models.analytics import AnalyticsEvent, AnalyticsSummary  # Added by task-1 for API imports
>>>>>>> origin/work/task-1-api-endpoints
```

**Resolution:**
Both workers added the same import (task-2 added the models, task-1 added the import for API use). The resolution is trivial - keep one copy:

```python
from models.user import User
from models.analytics import AnalyticsEvent, AnalyticsSummary
```

```bash
git add models/__init__.py
git commit -m "Resolve merge conflict: duplicate analytics import from task-1 and task-2"

# Continue merging task-3
git merge origin/work/task-3-frontend --no-ff -m "Merge task-3: Analytics frontend"
# Success - clean merge (frontend doesn't overlap with backend)

# Verify integration
git log --oneline --graph -15
```

### Final Integration Log

```
*   def4567 Merge task-3: Analytics frontend
|\
| * ghi7890 [COMPLETE] Analytics frontend implemented
| * ghi6789 [BLOCKED:api-response-format] Cannot complete real-time updates
| * ghi5678 [CHECKPOINT] Implement chart components
| * ghi4567 [CHECKPOINT] Create analytics dashboard template
|/
*   cde3456 Merge task-1: Analytics API endpoints
|\
| * def6789 [COMPLETE] Analytics API endpoints implemented
| * def5678 [CHECKPOINT] Add events endpoint with mock data
| * def4567 [NEEDS:task-2/AnalyticsEvent] Summary endpoint needs Analytics model
| * def3456 [CHECKPOINT] Create analytics API blueprint structure
|/
*   bcd2345 Resolve merge conflict: duplicate analytics import
*   abc1234 Merge task-2: Analytics models and migrations
|\
| * abc7890 [COMPLETE] Analytics models with tests
| * abc6789 [CHECKPOINT] Generate analytics migration
| * abc5678 [CHECKPOINT] Add AnalyticsSummary model
| * abc4567 [CHECKPOINT] Add AnalyticsEvent model
|/
* xyz0000 Previous main commit
```

### Final Checklist

- [x] All worker branches merged
- [x] All conflicts resolved
- [x] Integration branch created: `integration/analytics-dashboard`
- [ ] Tests pass (user to verify)
- [ ] Code review completed (user to verify)
- [ ] Integration tested end-to-end (user to verify)

### Cleanup Commands (After Merge to Main)

```bash
# After user approves and merges to main
git checkout main
git merge integration/analytics-dashboard --no-ff -m "Merge analytics-dashboard: parallel implementation complete"
git push origin main

# Remove worktrees
git worktree remove worktrees/task-1-api-endpoints
git worktree remove worktrees/task-2-models-migrations
git worktree remove worktrees/task-3-frontend

# Delete local branches
git branch -d work/task-1-api-endpoints
git branch -d work/task-2-models-migrations
git branch -d work/task-3-frontend
git branch -d integration/analytics-dashboard

# Delete remote branches (optional)
git push origin --delete work/task-1-api-endpoints
git push origin --delete work/task-2-models-migrations
git push origin --delete work/task-3-frontend
```

---

## Validation Checklist

This test scenario validates:

1. **Consistent commit prefixes** - Both skills use identical prefixes:
   - `[CHECKPOINT]`, `[BLOCKED:<reason>]`, `[NEEDS:task-X/<item>]`, `[COMPLETE]`

2. **Consistent naming conventions** - Both skills use:
   - Worktree: `worktrees/task-<id>-<description>/`
   - Branch: `work/task-<id>-<description>`

3. **Cross-dependency handling** - Demonstrated:
   - Worker 1 used `[NEEDS:task-2/AnalyticsEvent]`
   - Orchestrator detected and coordinated resolution

4. **Blocked scenario** - Demonstrated:
   - Worker 3 used `[BLOCKED:api-response-format]`
   - Orchestrator tracked and planned resolution

5. **Integration flow** - Demonstrated:
   - Sequential merges respecting dependencies
   - Merge conflict resolution
   - Final cleanup

6. **Worker kickoff prompts** - Include all required elements:
   - Task ID, scope, tasks, checkpoints, dependencies, commit protocol
