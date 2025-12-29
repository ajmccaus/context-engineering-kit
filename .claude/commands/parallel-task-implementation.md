---
description: Reorganize task implementation steps for maximum parallelization with explicit dependencies
argument-hint: Task ID (e.g., cek-673b) or 'latest' for most recent task
---

# Parallel Task Implementation Command

<task>
You are a task parallelization specialist. Analyze implementation steps in minibeads tasks and reorganize them for maximum parallel execution with explicit dependency tracking.
</task>

<context>
This command transforms sequential implementation plans into parallelized execution plans by:

1. Analyzing dependencies between implementation steps
2. Identifying steps that can execute in parallel
3. Adding explicit `Depends on:` and `Parallel with:` properties
4. Creating a visual dependency diagram
5. Grouping tightly coupled work into single steps
6. Using "MUST" language for parallel execution requirements
</context>

<workflow>

## Phase 1: Load and Understand Task

1. **Read the task file** to get full implementation process:

   ```bash
   Read .beads/issues/$TASK_ID.md
   ```

2. **Identify the Implementation Process section**:
   - Locate `## Implementation Process` or similar heading
   - List all current steps
   - Note any existing dependencies mentioned

## Phase 2: Analyze Step Dependencies

For each step, determine:

1. **Input requirements**:
   - What files/artifacts must exist before this step starts?
   - What information from previous steps is needed?

2. **Output artifacts**:
   - What does this step produce?
   - What files are created/modified?

3. **True dependencies vs. artificial sequencing**:
   - Does step B truly need step A's output?
   - Or were they just listed sequentially by habit?

4. **Identify parallel opportunities**:
   - Steps with the same dependencies can run in parallel
   - Independent utility work often parallelizes with main work

## Phase 3: Group Tightly Coupled Work

Identify steps that should be merged:

1. **Sync relationships**: If step A produces X and step B syncs X to Y, merge them
2. **Atomic operations**: Steps that must succeed together or fail together
3. **Same-file edits**: Multiple small edits to the same file → single step

**Example - Merge candidates:**

- "Update Plugin README" + "Sync Docs README from Plugin README" → Single step
- "Create config file" + "Update config file" → Single step

## Phase 4: Build Dependency Graph

Create a visual ASCII diagram showing:

```
Step 1 (Foundation)
    │
    ├─────────────────┬─────────────────┐
    ▼                 ▼                 ▼
Step 2a            Step 2b           Step 2c
(Can parallel)  (Can parallel)   (Can parallel)
    │                 │                 │
    └────────┬────────┘                 │
             ▼                          │
          Step 3                        │
     (Needs 2a, 2b)                     │
             │                          │
             └────────────┬─────────────┘
                          ▼
                       Step 4
                   (Needs 3, 2c)
```

**Diagram rules:**

- Vertical lines (│) show sequential dependency
- Horizontal branches (├──┬──┐) show parallel opportunities
- Merge points (└──┬──┘) show synchronization barriers

## Phase 5: Restructure Implementation Steps

Rewrite each step with:

### Step N: [Title]

**Depends on:** [List of step numbers, or "None"]
**Parallel with:** [List of step numbers that share same dependencies]
**Note:** [If contains parallelizable sub-tasks] Individual [items] MUST be [action] in parallel by multiple agents

[Step description]

#### Expected Output

- [Artifact 1]
- [Artifact 2]

#### Success Criteria

- [Criterion 1]
- [Criterion 2]

---

**Important:**

- Use "MUST be done in parallel" not "can be done in parallel"
- Be explicit about what enables parallelization
- Add tables for sub-tasks that parallelize:

| Sub-task | Description | Can Parallel |
|----------|-------------|--------------|
| task-1   | Description | Yes |
| task-2   | Description | Yes |

## Phase 6: Update Task File

**Use Write tool to modify the task file directly**:

1. **Preserve everything before Implementation Process**
2. **Add execution directive** (EXACTLY this text, right after `## Implementation Process` heading):

   ```
   You MUST launch for each step separate agent, instead of performing all steps by self. And for each step that mentioned as parallel, you MUST launch separate agents in parallel.

   **CRITICAL:** For each agent you MUST provide path to task file and prompt which step he must implement. And requirement that agent should implement exactly it, not more, not less, not other steps.
   ```

3. **Add Parallelization Overview diagram**
4. **Rewrite steps with dependency properties**
5. **Add horizontal rules (---) between steps for clarity**
6. **Preserve everything after Implementation Process** (Blockers, Notes, etc.)

</workflow>

<parallelization_principles>

## Key Principles

### 0. Sub-Agent Execution (CRITICAL)

You MUST launch for each step separate agent, instead of performing all steps by self. And for each step that mentioned as parallel, you MUST launch separate agents in parallel.

This directive MUST be added to every parallelized task file, right after the `## Implementation Process` heading.

### 1. High-Level Structure First

Create orchestrating files (workflow commands, main configs) BEFORE detail files (tasks, sub-configs). This establishes the skeleton that parallel workers fill in.

### 2. Same-Dependency Parallelization

Steps that depend on the same prerequisite(s) MUST run in parallel:

```
Step 1 (create directories)
    │
    ├──────────┬──────────┐
    ▼          ▼          ▼

Step 2a     Step 2b    Step 3

(agent)    (workflow)  (utils)
```

### 3. Merge Tightly Coupled Steps

If Step A's output is immediately consumed by Step B with no other consumers, consider merging:

- ❌ Step 6a: Update plugin README
- ❌ Step 6b: Sync docs README from plugin README  
- ✅ Step 6a: Update plugin README + sync to docs README

### 4. Sub-task Parallelization

When a step contains multiple independent items, make parallelization explicit:

**Note:** Individual task files MUST be created in parallel by multiple agents

### 5. Dependency Notation

- `Depends on: None` - Can start immediately
- `Depends on: Step 1` - Single dependency
- `Depends on: Step 2a, Step 2b` - Multiple dependencies (waits for ALL)
- `Parallel with: Step 2b, Step 3` - Same dependencies, run together

</parallelization_principles>

<common_patterns>

## Common Parallelization Patterns

### Pattern 1: Directory Setup → Parallel File Creation

```
Step 1: Create directories
    │
    ├──────────┬──────────┐
    ▼          ▼          ▼

Step 2a     Step 2b     Step 3
(agents)  (commands)   (utils)
```

### Pattern 2: Definition → Implementation → Manifest

```
Step 2a + 2b (definitions, parallel)
    │
    ▼
Step 3 (implementations using definitions)
    │

    ▼
Step 4 (manifest referencing all)
```

### Pattern 3: Implementation → Documentation → Cleanup

```
Step 4 (all implementations)
    │
    ├──────────┬
    ▼          ▼
Step 5a     Step 5b
(README)   (other docs)
    │          │
    └────┬─────┘
         ▼

      Step 6

    (cleanup)
```

### Pattern 4: Independent Utility Work

Utility/maintenance work often has minimal dependencies:

```
Step 1
    │
    ├──────────┬──────────┐
    ▼          ▼          ▼
Step 2      Step 3      Step 4
(main)     (main)    (utilities)
    │          │          │
    └────┬─────┘          │
         │                │
         └───────┬────────┘
                 ▼
              Step 5
```

</common_patterns>

<checklist>
Before completing parallelization:

- [ ] **Sub-agent execution directive added** (exact text after `## Implementation Process`)
- [ ] **All steps have `Depends on:` property**
- [ ] **Parallel opportunities identified with `Parallel with:`**
- [ ] **Visual dependency diagram added**
- [ ] **"MUST" used for parallel execution requirements** (not "can")
- [ ] **Tightly coupled steps merged** (no artificial splitting)
- [ ] **Sub-task tables include `Can Parallel` column**
- [ ] **High-level structure steps come before detail steps**
- [ ] **Horizontal rules (---) separate steps**
</checklist>

<example_session>
User: /parallel-task-implementation cek-31ce

**Phase 1: Loading task...**

```bash
mb show cek-31ce
```

Task: "Reorganize FPF plugin using workflow command pattern"

**Phase 2: Analyzing dependencies...**

Current steps (sequential):

1. Create Directory Structure
2. Create FPF Agent Definition
3. Create Task Files
4. Create propose-hypotheses Workflow Command
5. Rename and Simplify Utility Commands
6. Update Plugin Manifest
7. Update Documentation
8. Clean Up Old Commands

*Analyzing true dependencies...*

- Step 2 (Agent) needs: directories (Step 1)
- Step 3 (Tasks) needs: agent definition (Step 2), workflow structure (Step 4)
- Step 4 (Workflow) needs: directories (Step 1) ← NOT agent!
- Step 5 (Utils) needs: directories (Step 1) ← Independent!

*Identifying false dependencies...*

- Steps 2, 4, 5 all only depend on Step 1 → CAN PARALLEL
- Step 4 was listed after Step 3, but Step 3 depends on Step 4!

**Phase 3: Grouping tightly coupled work...**

- "Update Plugin README" + "Sync Docs README" → Merge into single step
- Step 6b and 6c shared same dependency → Consider merging if related

**Phase 4: Building dependency graph...**

```
Step 1 (Directory Structure)
    │
    ├───────────────────┬───────────────────┐
    ▼                   ▼                   ▼
Step 2a              Step 2b             Step 3
(FPF Agent)     (Workflow Command)   (Utility Commands)
    │                   │                   │
    └─────────┬─────────┘                   │
              ▼                             │
           Step 4                           │
       (Task Files)                         │
              │                             │
              └─────────────┬───────────────┘
                            ▼
                         Step 5
                    (Plugin Manifest)
                            │
    ┌───────────────────────┼
    ▼                       ▼
Step 6a                  Step 6b
(Plugin README)      (Other Docs)
    │                       │
    └───────────────────────┴
                            │
                            ▼
                         Step 7

                    (Clean Up Old Commands)
```

**Phase 5: Restructuring steps...**

Key changes:

- Workflow Command (2b) moved BEFORE Task Files
- Agent (2a) and Workflow (2b) now parallel
- Utility Commands (3) parallel with 2a, 2b
- Task Files now correctly depends on 2a AND 2b

- Documentation split into README (6a) + Other Docs (6b)
- Added "MUST be done in parallel" for sub-tasks

**Phase 6: Updating task file...**

Task updated with:

- Sub-agent execution directive added after `## Implementation Process`
- Parallelization Overview diagram
- 7 main steps (was 8, merged docs)
- Explicit `Depends on:` for each step
- `Parallel with:` annotations
- "MUST" language for parallel execution

</example_session>

<output>
After parallelization, report:

1. **Task Updated**: $TASK_ID
2. **Steps Reorganized**: X steps (from Y original)
3. **Steps Merged**: X steps combined
4. **Max Parallelization Depth**: X steps can run simultaneously at peak

Suggest: `mb show $TASK_ID` to review the parallelized task
</output>
