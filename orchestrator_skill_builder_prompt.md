# Orchestrator and Worker Skill Builder Prompt

This prompt was used to create the parallel-orchestrator and parallel-worker skills.

---

```
ULTRATHINK about this entire task before starting.

Create two tightly-coupled skill documents for parallel workflow orchestration.

## Skills to Create

### 1. Orchestrator Skill (`SKILL.md` in a `parallel-orchestrator/` directory)

This skill enables an agent to act as a programming manager that coordinates parallel workstreams using git worktrees.

**Phase 0: Input Analysis** (when receiving documents rather than direct instructions)
- Parse markdown documents (code reviews, issue lists, etc.) to extract work items
- Use graceful degradation: try task lists, then numbered lists, then bullets, then paragraphs
- Normalize items to standard format: ID, action, description, scope, complexity, dependencies
- Discover dependencies from explicit signals ("after X", "requires Y") and implicit signals (file relationships)
- Estimate complexity using T-shirt sizing (S/M/L/XL) based on scope and action type
- Assess parallelizability: recommend sequential if all items touch same file, linear dependencies, <3 items, or <2 hours total work
- Present Work Item Manifest for user validation before proceeding

**Phase 1: Planning**
- Analyze requested changes against repository structure
- Propose splitting work into N parallel workstreams
- Identify file/directory ownership boundaries to minimize conflicts
- Detect cross-dependencies upfront

**Phase 2: Setup**
- Create git worktrees: `worktrees/task-<id>-<short-description>/`
- Create branches: `work/<task-id>-<short-description>`
- Generate kickoff prompts for each worker session
- Specify checkpoint expectations in each kickoff prompt

**Phase 3: Monitoring**
- Fetch all worker branches to check progress
- Parse commit messages for status prefixes:
  - `[CHECKPOINT]` - subtask complete, continuing
  - `[BLOCKED:<reason>]` - worker cannot proceed
  - `[NEEDS:<worker-id>/<item>]` - cross-dependency discovered
  - `[COMPLETE]` - worker finished all assigned tasks
- Alert user if expected commits don't appear
- Reassign incomplete work from failed/stalled workers
- Coordinate cross-dependencies

**Phase 4: Integration**
- Create integration branch from main
- Merge all worker branches sequentially
- Resolve merge conflicts (or flag complex conflicts for user)
- Present final integration for user review

**Key Constraints:**
- Orchestrator never spawns processes directly; outputs commands/prompts for user
- Uses git as sole communication mechanism with workers
- Must handle wrong-branch commits (detection and recovery)

### 2. Worker Skill (`SKILL.md` in a `parallel-worker/` directory)

This skill enables an agent to act as a focused implementer.

**Execution:**
- Receives kickoff prompt specifying: assigned files, tasks, checkpoints, branch name
- Works exclusively within assigned scope
- Makes incremental commits with detailed messages
- Uses commit prefix conventions consistently

**Checkpointing:**
- Commits at orchestrator-specified granularity
- Every commit starts with prefix: `[CHECKPOINT]`, `[BLOCKED:<reason>]`, `[NEEDS:<worker-id>/<item>]`, or `[COMPLETE]`
- Commit bodies contain detail for orchestrator to understand progress

**Scope Discipline:**
- Never modifies files outside assigned boundaries
- When discovering out-of-scope dependencies: commits with `[NEEDS:...]`, documents what's needed, continues other subtasks if possible
- Always verifies current branch before committing

**Completion:**
- Final commit uses `[COMPLETE]` prefix
- Includes summary of all changes
- Notes any issues or recommendations

**Key Constraints:**
- Worker assumes orchestrator handles coordination
- Worker doesn't merge anything; only commits to assigned branch
- Worker must verify branch context before every commit

## Consistency Requirements

Both skills must use identical:
- Commit prefix conventions
- Worktree/branch naming conventions
- Workflow stage assumptions
- Cross-references to each other

## Test Scenario

Create a test scenario document (`parallel-workflow-TEST_SCENARIO.md`) showing:
1. A 3-worker task split for a Python web application feature
2. Example kickoff prompts
3. Example commit sequences including blocked and cross-dependency scenarios
4. Example orchestrator monitoring output
5. Example integration with merge conflict resolution

## File Locations

- `~/.claude/skills/parallel-orchestrator/SKILL.md`
- `~/.claude/skills/parallel-worker/SKILL.md`
- `~/.claude/skills/parallel-workflow-TEST_SCENARIO.md`
```
