# Retrospective Skill Builder Prompt

This prompt was used to create the parallel-retrospective skill.

---

```
ULTRATHINK about this entire task before starting.

Read the two skills you previously created to ensure consistency:
- `~/.claude/skills/parallel-orchestrator/SKILL.md`
- `~/.claude/skills/parallel-worker/SKILL.md`

Create a retrospective skill that evaluates parallel workflow executions.

## Skill to Create

### Retrospective Skill (`SKILL.md` in a `parallel-retrospective/` directory)

This skill enables an agent to act as a workflow analyst that reviews completed (or failed) parallel workflow sessions.

**Inputs:**
- Required: Worker branches and/or integration branch
- Required: Original task description given to orchestrator
- Optional: User notes about issues encountered
- Optional: Work Item Manifest from orchestrator's Phase 0 (enables Planning Quality Assessment)

**Analysis Phase:**

The retrospective examines six dimensions:

1. **Commit History Analysis**
   - Parse all commits across worker branches
   - Reconstruct timeline (who committed what, when)
   - Check prefix usage: `[CHECKPOINT]`, `[BLOCKED]`, `[NEEDS]`, `[COMPLETE]`
   - Detect wrong-branch commits
   - Measure checkpoint frequency

2. **Work Distribution Assessment**
   - Was work split evenly?
   - Did any worker finish significantly earlier/later?
   - Were file/directory boundaries respected?
   - How many cross-dependencies occurred?

3. **Blocking & Failure Analysis**
   - How many `[BLOCKED]` commits? Reasons?
   - Were blocks resolved? How long?
   - Did any workers stall without signaling?
   - Any work reassignments?

4. **Integration Quality**
   - How many merge conflicts?
   - Were conflicts from poor scope or unavoidable overlap?
   - Was manual intervention required?

5. **Convention Adherence**
   - Did branches follow naming conventions?
   - Were commit messages detailed enough?
   - Did workers verify branch context?

6. **Planning Quality Assessment** (requires Work Item Manifest)
   - Complexity accuracy: Compare estimated vs actual (commits, time, blocks)
   - Grouping effectiveness: Did workers finish within 2x of each other?
   - Parallelization decision: Was parallel the right choice? Calculate blocked time ratio
   - Assumption validation: Did documented assumptions hold?

**Output: Retrospective Report**

1. **Summary**: Grade (A-F), assessment, key metrics
2. **What Worked Well**: Examples with commit references
3. **Problems Identified**: Categorized (planning/execution/communication/integration), severity-rated
4. **Root Cause Analysis**: Skill design flaw / Orchestrator decision / Worker error / Unavoidable complexity
5. **Recommendations**: Skill updates, process improvements, tooling gaps
6. **Proposed Skill Patches**: Specific diffs with rationale

**Key Constraints:**
- Read-only: never modifies branches or commits
- Evidence-based: cite specific commits
- Concrete recommendations: not "communicate better" but "add checkpoint after X"
- Distinguish one-off mistakes from systemic issues

**Invocation Modes:**
1. Post-completion review
2. Post-failure analysis
3. Mid-flight check (advisory)

**Grading Rubric:**
- A: All complete, no blocks, no conflicts, conventions perfect
- B: All complete, 1-2 minor issues
- C: All complete with multiple issues
- D: Significant issues, stalls, scope violations
- F: Failed or abandoned

## Consistency Requirements

- Same commit prefixes as orchestrator/worker
- Same naming conventions
- Reference orchestrator and worker skills by path
- Include enough context for standalone use

## Test Scenario

Create `parallel-retrospective-TEST_SCENARIO.md` showing:
1. Mixed-results workflow (smooth completion, blocked, cross-dependency, merge conflict, wrong-branch recovery)
2. Example retrospective report with grade assignment, root cause analysis, and proposed skill patch

## File Locations

- `~/.claude/skills/parallel-retrospective/SKILL.md`
- `~/.claude/skills/parallel-retrospective-TEST_SCENARIO.md`
```
