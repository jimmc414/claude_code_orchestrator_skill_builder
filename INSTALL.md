# Installation

## Option 1: Install Prompt (Recommended)

Copy this entire prompt and paste it into Claude Code:

```
Install the parallel workflow skills from this repository to ~/.claude/skills/.

The repository contains three skills:
- parallel-orchestrator/ - for splitting work and coordinating
- parallel-worker/ - for executing assigned tasks
- parallel-retrospective/ - for analyzing completed workflows

For each skill directory, copy the entire folder to ~/.claude/skills/.

After copying, verify the installation by listing:
ls -la ~/.claude/skills/parallel-*/

Expected output should show three directories:
- parallel-orchestrator/
- parallel-worker/
- parallel-retrospective/

Each containing a SKILL.md file.
```

## Option 2: Manual Installation

```bash
# Clone the repository
git clone https://github.com/jimmc414/claude_code_orchestrator_skill_builder.git
cd claude_code_orchestrator_skill_builder

# Create skills directory if it doesn't exist
mkdir -p ~/.claude/skills

# Copy skill folders
cp -r parallel-orchestrator ~/.claude/skills/
cp -r parallel-worker ~/.claude/skills/
cp -r parallel-retrospective ~/.claude/skills/

# Verify
ls ~/.claude/skills/parallel-*/SKILL.md
```

## Option 3: Generate Fresh Skills with Builder Prompts

Instead of copying pre-built skills, have Claude Code generate them from the builder prompts. This lets you customize conventions during creation.

**Generate orchestrator and worker skills:**
```
Read @orchestrator_skill_builder_prompt.md and follow the instructions to create
the parallel-orchestrator and parallel-worker skills in ~/.claude/skills/.
```

**Generate retrospective skill:**
```
Read @orchestrator_retrospective_skill_builder_prompt.md and follow the
instructions to create the parallel-retrospective skill in ~/.claude/skills/.
```

**Generate with customizations:**
```
Read @orchestrator_skill_builder_prompt.md and create the skills with these
modifications:
- Use [WIP] instead of [CHECKPOINT] for commit prefixes
- Use feature/ instead of work/ for branch prefixes
- Add a requirement that workers run tests before marking [COMPLETE]
```

This approach is useful when you want to:
- Modify commit prefix conventions
- Change branch naming patterns
- Add team-specific workflow requirements
- Extend skills with additional capabilities

## Verification

After installation, test that Claude recognizes the skills:

```
You: What parallel workflow skills do you have available?

Claude: I have access to three parallel workflow skills:
        - parallel-orchestrator: for splitting work across multiple sessions
        - parallel-worker: for executing tasks in a worktree
        - parallel-retrospective: for analyzing completed workflows
```

## Usage Examples

### Example 1: Parallel Code Review Fixes

```
You: I have this code review with issues to fix:

## High Priority
- [ ] Fix null pointer in user.py line 42
- [ ] Add input validation to api/auth.py

## Medium Priority
- [ ] Refactor duplicate code in utils/
- [ ] Add tests for payment module

Use the parallel-orchestrator skill to split this into parallel workstreams.
```

### Example 2: Monitor Worker Progress

```
You: Check on the parallel workers. Show me their status.
```

### Example 3: Run Retrospective

```
You: The parallel workflow is complete. Run a retrospective analysis
     to see what we can improve for next time.
```

## Uninstallation

```bash
rm -rf ~/.claude/skills/parallel-orchestrator
rm -rf ~/.claude/skills/parallel-worker
rm -rf ~/.claude/skills/parallel-retrospective
```
