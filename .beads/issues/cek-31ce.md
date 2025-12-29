title: Reorganize FPF plugin using workflow command pattern
status: open
priority: 2
issue_type: task
created_at: 2025-12-29T00:00:46.409297392+00:00
updated_at: 2025-12-29T00:06:05.064110726+00:00
-----------------------------------------------

# Description

**Initial User Prompt:**

> Current commands for @plugins/fpf/commands/ plugin was copied from another project. As a result they not very effectively organized. Read @plugins/customaize-agent/commands/create-workflow-command.md and use it to reorganize this plugin. ultrathink during task refinement. AC should include creation of propose-hypotheses workflow command that will use new fpf-agent that will execute tasks. Write during refinement step high-level structure of plugin and workflow high-level steps. Important to split responsibilities properly. For example creation of folder structure from @plugins/fpf/commands/q0-init.md should be done by main agent from workflow command. While hypothesis writing from @q1-hypothesize.md should be done by agent. Also, after initial hypothesis writing main agent should present short summary of hypotheses then ask whether user want to add some hypotheses, if so then use q1-add task, then continue with the rest of the workflow. Also analyse what other q-* commands used for and propose whether they should be separate plugin command or part of workflow or something other, if they not really useful in final implementation.

---

Reorganize the FPF (First Principles Framework) plugin from its current flat command structure (12 separate commands copied from quint-code) to use the workflow command pattern with proper orchestrator/sub-agent responsibility separation.

**Acceptance Criteria:**

- Create `propose-hypotheses` workflow command that orchestrates the hypothesis generation phase
- Create `fpf-agent` specialized sub-agent for FPF reasoning tasks
- Create task files in `tasks/` directory for sub-agent execution
- Main agent handles: folder structure creation, user interaction, summaries
- FPF agent handles: hypothesis generation, formalization, verification logic
- After initial hypothesis generation, main agent presents summary and asks if user wants to add more
- If user wants to add, launch fpf-agent with add-hypothesis task, then loop back
- Utility commands (status, query, decay, actualize, reset) remain as standalone commands
- Core workflow commands (verify, validate, audit, decide) converted to workflows tasks

**Example Flow for propose-hypotheses:**

1. User: `/fpf:propose-hypotheses What caching strategy should we use?`
2. Main agent creates .fpf/ structure if needed
3. Main agent launches fpf-agent → generates 3-5 L0 hypotheses
4. Main agent presents summary table
5. Main agent asks: 'Would you like to add any hypotheses?'
6. If yes: fpf-agent adds user hypothesis → return to step 4
7. If no: Main agent uses subagent to perform rest of tasks: verify, validate, audit, decide.
8. After all tasks are completed, main agent presents final summary.

---

## Files to be Modified/Created

### Primary Changes (New Files)

```
plugins/fpf/
├── agents/
│   └── fpf-agent.md              # NEW: FPF reasoning specialist agent definition
├── commands/
│   └── propose-hypotheses.md     # NEW: Main workflow orchestrator for complete FPF cycle
└── tasks/
    ├── init-context.md           # NEW: Initialize context and problem framing task
    ├── generate-hypotheses.md    # NEW: Generate L0 hypotheses task
    ├── add-user-hypothesis.md    # NEW: Add user's hypothesis task
    ├── verify-logic.md           # NEW: Verify single L0 → L1 task (per hypothesis)
    ├── validate-evidence.md      # NEW: Validate single L1 → L2 task (per hypothesis)
    ├── audit-trust.md            # NEW: Compute R_eff task (per hypothesis)
    └── decide.md                 # NEW: Create DRR task
```

### Commands to Simplify/Rename

```
plugins/fpf/commands/
├── q-status.md    → status.md      # Simplified utility command
├── q-query.md     → query.md       # Simplified utility command
├── q-decay.md     → decay.md       # Simplified utility command
├── q-actualize.md → actualize.md   # Simplified utility command
└── q-reset.md     → reset.md       # Simplified utility command
```

### Commands to Delete (Merged into Workflow Tasks)

```
plugins/fpf/commands/
├── q0-init.md         # DELETE: Logic moved to init-context.md task
├── q1-hypothesize.md  # DELETE: Logic moved to generate-hypotheses.md task
├── q1-add.md          # DELETE: Logic moved to add-user-hypothesis.md task
├── q2-verify.md       # DELETE: Logic moved to verify-logic.md task
├── q3-validate.md     # DELETE: Logic moved to validate-evidence.md task
├── q4-audit.md        # DELETE: Logic moved to audit-trust.md task
└── q5-decide.md       # DELETE: Logic moved to decide.md task
```

### Configuration Changes

```
plugins/fpf/
├── .claude-plugin/
│   └── plugin.json   # UPDATE: Update commands list, add agent
└── README.md         # UPDATE: Update documentation for new structure
```

### Documentation Updates

```
README.md                                   # UPDATE: Update README.md with plugin description and structure
docs/
├── plugins/
│   └── fpf/
│       └── README.md                       # UPDATE: Update plugin documentation
├── concepts.md                             # UPDATE: Update concepts documentation
├── guides/
│   └── decision-making.md                  # UPDATE: Update hypothesis generation guide
|   └── brainstorming-to-implementation.md  # UPDATE: Update brainstorming to implementation guide
├── resources/
│   └── related-projects.md                 # UPDATE: Update related projects documentation
└── reference/
    └── commands.md                         # UPDATE: Update commands documentation
```

---

## Useful Resources for Implementation

### Pattern References

```
plugins/
├── customaize-agent/
│   └── commands/
│       └── create-workflow-command.md   # Workflow command pattern guide
├── sdd/
│   ├── agents/
│   │   └── software-architect.md        # Agent definition pattern
│   └── commands/
│       └── 02-plan.md                   # Multi-agent orchestration example
└── code-review/
    └── commands/
        └── review-pr.md                 # Workflow with multiple sub-agents
```

### Documentation

```
docs/
├── guides/
│   └── custom-extensions.md             # How to create plugins
└── reference/
    └── agents.md                        # Agent documentation standards
```

### Related Code

```
plugins/
├── sdd/
│   └── agents/                          # Multiple agent examples (7 agents)
└── code-review/
    └── agents/                          # Review agent examples (6 agents)
```

---

## High-Level Plugin Structure After Reorganization

```
plugins/fpf/
├── .claude-plugin/
│   └── plugin.json              # Plugin manifest (updated)
├── README.md                    # Plugin documentation (updated)
├── agents/
│   └── fpf-agent.md            # FPF reasoning specialist
├── commands/
│   ├── propose-hypotheses.md   # Workflow: Complete FPF cycle (hypothesis → decision)
│   ├── status.md               # Show FPF status (utility)
│   ├── query.md                # Search knowledge base (utility)
│   ├── decay.md                # Evidence freshness (utility)
│   ├── actualize.md            # Sync with codebase (utility)
│   └── reset.md                # Reset session (utility)
└── tasks/
    ├── init-context.md           # Task: Initialize context and problem framing
    ├── generate-hypotheses.md    # Task: Generate L0 hypotheses
    ├── add-user-hypothesis.md    # Task: Add user's hypothesis
    ├── verify-logic.md           # Task: Verify single L0 → L1 (per hypothesis, parallel)
    ├── validate-evidence.md      # Task: Validate single L1 → L2 (per hypothesis, parallel)
    ├── audit-trust.md            # Task: Compute R_eff (per hypothesis, parallel)
    └── decide.md                 # Task: Create DRR
```

---

## propose-hypotheses Workflow High-Level Steps

### Step 1a: Create Directory Structure (Main Agent)

**Responsibility:** Create directory structure if needed
**Actions:**

- Check if `.fpf/` directory exists
- If not: create directory scaffold (`.fpf/knowledge/L0`, `.fpf/knowledge/L1`, `.fpf/decisions/`, etc.)

**Why main agent:** Simple file operations, no FPF reasoning needed

### Step 1b: Generate Context (FPF Agent via Task)

**Responsibility:** Generate bounded context for the problem
**Prompt:** Read \${CLAUDE_PLUGIN_ROOT}/tasks/init-context.md
**Actions:**

- Analyze user's problem/question to frame the bounded context
- Generate and write `.fpf/context.md` with problem framing, constraints, scope
- Return context summary

**Why sub-agent:** Context framing requires FPF reasoning to properly bound the problem space

### Step 2: Generate Hypotheses (FPF Agent via Task)

**Responsibility:** Generate diverse L0 hypotheses
**Prompt:** Read \${CLAUDE_PLUGIN_ROOT}/tasks/generate-hypotheses.md
**Actions:**

- Frame the anomaly/problem from user input
- Generate 3-5 diverse hypotheses (conservative + radical)
- Create L0 hypothesis files in `.fpf/knowledge/L0/`
- Return summary of generated hypotheses
  **Why sub-agent:** Complex FPF reasoning, specialized knowledge

### Step 3: Present Summary (Main Agent)

**Responsibility:** User interaction loop
**Actions:**

- Read generated L0 hypothesis files
- Present summary table (ID, title, kind, scope)
- Ask user: 'Would you like to add any hypotheses of your own?'

### Step 4: Add User Hypothesis (FPF Agent via Task, conditional)

**Condition:** User says yes to adding hypotheses
**Prompt:** Read \${CLAUDE_PLUGIN_ROOT}/tasks/add-user-hypothesis.md
**Actions:**

- Get user's hypothesis description
- Formalize into proper hypothesis file format
- Create L0 file in `.fpf/knowledge/L0/`
- Return to Step 3 (loop)

### Step 5: Verify Logic (FPF Agents via Task, parallel)

**Condition:** User says no to adding more hypotheses
**Prompt:** Read \${CLAUDE_PLUGIN_ROOT}/tasks/verify-logic.md
**Execution:** Launch separate sub-agent for EACH L0 hypothesis (parallel)
**Actions per hypothesis:**

- Perform logical verification on single hypothesis
- Check internal consistency, apply first-principles reasoning
- Create L1 verified hypothesis file in `.fpf/knowledge/L1/`
- Return verification result for this hypothesis

**Why parallel sub-agents:** Each hypothesis can be verified independently; enables concurrent processing

### Step 6: Validate Evidence (FPF Agents via Task, parallel)

**Prompt:** Read \${CLAUDE_PLUGIN_ROOT}/tasks/validate-evidence.md
**Execution:** Launch separate sub-agent for EACH L1 hypothesis (parallel)
**Actions per hypothesis:**

- Gather supporting evidence for single hypothesis
- Check against codebase, documentation, external sources
- Create L2 validated hypothesis file in `.fpf/knowledge/L2/`
- Return validation result with confidence score for this hypothesis

**Why parallel sub-agents:** Each hypothesis can be validated independently; enables concurrent evidence gathering

### Step 7: Audit Trust (FPF Agents via Task, parallel)

**Prompt:** Read \${CLAUDE_PLUGIN_ROOT}/tasks/audit-trust.md
**Execution:** Launch separate sub-agent for EACH L2 hypothesis (parallel)
**Actions per hypothesis:**

- Compute R_eff (effective reliability) for single hypothesis
- Apply decay factors for evidence freshness
- Calculate confidence interval
- Return trust audit result for this hypothesis

**Why parallel sub-agents:** Each hypothesis trust calculation is independent; enables concurrent computation

### Step 8: Make Decision (FPF Agent via Task)

**Prompt:** Read \${CLAUDE_PLUGIN_ROOT}/tasks/decide.md
**Actions:**

- Create Decision Readiness Report (DRR)
- Rank hypotheses by R_eff and confidence
- Generate recommended action with rationale
- Save decision artifacts to `.fpf/decisions/`
  **Why sub-agent:** Synthesis requires full FPF context

### Step 9: Present Final Summary (Main Agent)

**Responsibility:** Present results to user
**Actions:**

- Read DRR and decision artifacts
- Present ranked hypotheses with confidence scores
- Show recommended decision with supporting evidence
- Suggest next steps or follow-up actions

---

## Command Analysis: Keep vs Convert vs Delete

| Original Command | New Name               | Decision        | Rationale                                       |
| ------------------ | ------------------------ | ----------------- | ------------------------------------------------- |
| q0-init          | init-context.md        | Task file       | Context framing requires FPF reasoning          |
| q1-hypothesize   | generate-hypotheses.md | Task file       | Core hypothesis generation done by fpf-agent    |
| q1-add           | add-user-hypothesis.md | Task file       | User hypothesis formalization done by fpf-agent |
| q2-verify        | verify-logic.md        | Task file       | Logical verification is part of full FPF cycle  |
| q3-validate      | validate-evidence.md   | Task file       | Evidence validation is part of full FPF cycle   |
| q4-audit         | audit-trust.md         | Task file       | Trust audit is part of full FPF cycle           |
| q5-decide        | decide.md              | Task file       | Decision making completes the FPF cycle         |
| q-status         | status                 | Keep simplified | Utility, no orchestration needed                |
| q-query          | query                  | Keep simplified | Utility, no orchestration needed                |
| q-decay          | decay                  | Keep simplified | Utility, no orchestration needed                |
| q-actualize      | actualize              | Keep simplified | Utility, no orchestration needed                |
| q-reset          | reset                  | Keep simplified | Utility, no orchestration needed                |

---

## Implementation Process

### Parallelization Overview

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

---

### Step 1: Create Directory Structure

**Depends on:** None

Create `agents/` and `tasks/` directories in the FPF plugin.

#### Expected Output

- `plugins/fpf/agents/` directory exists
- `plugins/fpf/tasks/` directory exists

#### Success Criteria

- Directories created

#### Verification

**Level:** ❌ NOT NEEDED
**Rationale:** Simple file operation. Success is binary (directories exist or don't). File system check sufficient.

---

### Step 2a: Create FPF Agent Definition

**Depends on:** Step 1

Write `plugins/fpf/agents/fpf-agent.md` based on existing agent patterns.

#### Expected Output

- Agent file with proper frontmatter (name, description, tools)
- Core instructions for FPF reasoning
- Knowledge layer awareness (L0/L1/L2)
- File format specifications for hypotheses

#### Success Criteria

- Agent follows pattern from `plugins/sdd/agents/software-architect.md`
- Includes RFC 2119 bindings for file operations
- Documents hypothesis file format

#### Verification

**Level:** ✅ CRITICAL - Panel of 2 Judges with Aggregated Voting
**Artifact:** `plugins/fpf/agents/fpf-agent.md`
**Threshold:** 4.0/50
**Judge Agent:** `.claude/agents/judge.md`

**Rubric:**

| Criterion | Weight | Description |
|-----------|--------|-------------|
| Pattern Conformance | 0.25 | Follows agent pattern from `plugins/sdd/agents/software-architect.md` |
| Frontmatter Completeness | 0.20 | Has name, description, tools fields |
| FPF Domain Knowledge | 0.25 | Demonstrates understanding of L0/L1/L2 knowledge layers |
| Hypothesis File Format | 0.15 | Documents hypothesis file format clearly |
| RFC 2119 Bindings | 0.15 | Uses MUST/SHOULD/MAY for file operations |

**Reference Pattern:** `plugins/sdd/agents/software-architect.md`

---

### Step 2b: Create propose-hypotheses Workflow Command

**Depends on:** Step 1
**Parallel with:** Step 2a, Step 3

Write `plugins/fpf/commands/propose-hypotheses.md` orchestrator for complete FPF cycle. This defines the high-level structure before task files are created.

#### Expected Output

- Frontmatter with description, allowed-tools (Task, Read, Write, Bash, AskUserQuestion)
- 10-step workflow: create dirs (1a) → init context (1b) → generate → present → (add loop) → verify → validate → audit → decide → summary
- User interaction points: hypothesis review loop (steps 3-4)
- Context passing between steps via `.fpf/` directory state
- References to task files (paths defined, files created later)

#### Success Criteria

- Orchestrator is lean (~50-100 tokens per step)
- Uses \${CLAUDE_PLUGIN_ROOT}/tasks/ paths for fpf-agent dispatches
- Step 1a (create dirs) done by main agent directly
- Steps 1b, 2, 4, 8 dispatch to fpf-agent via Task tool
- Steps 5, 6, 7 launch parallel sub-agents (one per hypothesis)
- Steps 3, 9 are main agent summaries/user interaction
- Includes loop for adding user hypotheses (steps 3-4)
- Ends with final decision summary and next steps

#### Verification

**Level:** ✅ CRITICAL - Panel of 2 Judges with Aggregated Voting
**Artifact:** `plugins/fpf/commands/propose-hypotheses.md`
**Threshold:** 4.0/50
**Judge Agent:** `.claude/agents/judge.md`

**Rubric:**

| Criterion | Weight | Description |
|-----------|--------|-------------|
| Orchestrator Leanness | 0.20 | ~50-100 tokens per step dispatch |
| Task Path References | 0.15 | Uses `${CLAUDE_PLUGIN_ROOT}/tasks/` correctly |
| Step Responsibility | 0.25 | Correct main agent vs sub-agent assignments |
| User Interaction | 0.15 | Hypothesis review loop (steps 3-4) implemented |
| Parallel Execution | 0.15 | Steps 5,6,7 launch parallel sub-agents |
| Completion Flow | 0.10 | Final summary and next steps present |

**Reference Pattern:** `plugins/customaize-agent/commands/create-workflow-command.md`

---

### Step 3: Rename and Simplify Utility Commands

**Depends on:** Step 1
**Parallel with:** Step 2a, Step 2b

Rename q-* utility commands and simplify where possible.

#### Expected Output

- Renamed files: status.md, query.md, decay.md, actualize.md, reset.md
- Simplified descriptions where appropriate
- Consistent naming without q- prefix

#### Verification

**Level:** ✅ Per-Command Judges (5 separate judges in parallel)
**Artifacts:** `plugins/fpf/commands/{status,query,decay,actualize,reset}.md`
**Threshold:** 4.0/50
**Judge Agent:** `.claude/agents/judge.md`

**Rubric (per command):**

| Criterion | Weight | Description |
|-----------|--------|-------------|
| Correct Renaming | 0.30 | q-prefix removed, file renamed correctly |
| Description Updated | 0.25 | Description reflects utility purpose |
| Functionality Preserved | 0.25 | Core logic unchanged |
| Simplification | 0.20 | Unnecessary complexity removed |

**Execution:** Launch 5 judge agents in parallel (one per command).

---

### Step 4: Create Task Files

**Depends on:** Step 2a, Step 2b
**Note:** Individual task files MUST be created in parallel by multiple agents

Write task files for sub-agent execution:

| Task File | Description | Can Parallel |
|-----------|-------------|--------------|
| `init-context.md` | Initialize directory structure and generate bounded context | Yes |
| `generate-hypotheses.md` | Generate diverse L0 hypotheses | Yes |
| `add-user-hypothesis.md` | Formalize user's hypothesis | Yes |
| `verify-logic.md` | Logical verification of single hypothesis L0 → L1 | Yes |
| `validate-evidence.md` | Evidence gathering for single hypothesis L1 → L2 | Yes |
| `audit-trust.md` | Compute R_eff for single hypothesis | Yes |
| `decide.md` | Create DRR and recommendation (aggregates all hypotheses) | Yes |

#### Expected Output

- Task files with clear context, goal, instructions, constraints, expected output
- Each task is self-contained (sub-agent doesn't need external context)
- Success criteria with checkboxes

#### Success Criteria

- Tasks follow pattern from `create-workflow-command.md` example
- Each task ~500+ tokens of detailed instructions
- Clear input/output contracts

#### Verification

**Level:** ✅ Per-Task Judges (7 separate judges in parallel)
**Artifacts:** `plugins/fpf/tasks/{init-context,generate-hypotheses,add-user-hypothesis,verify-logic,validate-evidence,audit-trust,decide}.md`
**Threshold:** 4.0/50
**Judge Agent:** `.claude/agents/judge.md`

**Rubric (per task file):**

| Criterion | Weight | Description |
|-----------|--------|-------------|
| Self-Containment | 0.25 | Sub-agent doesn't need external context |
| Context Section | 0.15 | Clear context for what step this is |
| Goal Clarity | 0.20 | Specific, measurable goal |
| Instructions Quality | 0.20 | Numbered, actionable steps |
| Success Criteria | 0.15 | Checkboxes with measurable outcomes |
| Input/Output Contract | 0.05 | Clear what goes in and comes out |

**Reference Pattern:** `plugins/customaize-agent/commands/create-workflow-command.md` (task file example)

**Execution:** Launch 7 judge agents in parallel (one per task file).

---

### Step 5: Update Plugin Manifest

**Depends on:** Step 2a, Step 2b, Step 3

Update `plugins/fpf/.claude-plugin/plugin.json` with new structure.

#### Expected Output

- Updated commands list (propose-hypotheses + utility commands)
- Added agent to plugin manifest
- Version bump

#### Verification

**Level:** ❌ NOT NEEDED
**Rationale:** Simple JSON update. Validation is schema-based (valid JSON, correct structure). Binary success/failure.

---

### Step 6a: Update Plugin README

**Depends on:** Step 5
**Parallel with:** Step 6b, Step 6c

Update `plugins/fpf/README.md` with new structure and usage.
Sync `docs/plugins/fpf/README.md` with `plugins/fpf/README.md`.

#### Expected Output

- Plugin description updated
- New command structure documented
- Agent documented
- Task files documented
- Synced with plugin README
- User-facing documentation updated

#### Verification

**Level:** ✅ CRITICAL - Panel of 2 Judges with Aggregated Voting
**Artifacts:** `plugins/fpf/README.md`, `docs/plugins/fpf/README.md`
**Threshold:** 4.0/50
**Judge Agent:** `.claude/agents/judge.md`

**Rubric:**

| Criterion | Weight | Description |
|-----------|--------|-------------|
| Structure Completeness | 0.25 | All sections present (description, commands, agents, tasks) |
| Command Documentation | 0.20 | All commands listed with descriptions |
| Agent Documentation | 0.15 | Agent role and usage documented |
| Task Documentation | 0.15 | Task files documented with purposes |
| Sync Accuracy | 0.15 | `docs/plugins/fpf/README.md` matches plugin README |
| Usage Examples | 0.10 | At least one example workflow |

**Note:** Both files should be synced (or intentionally different with documented reason).

---

### Step 6b: Update Other Documentation Files

**Depends on:** Step 5
**Parallel with:** Step 6a, Step 6b
**Note:** Individual doc files MUST be updated in parallel by multiple agents

Update all related documentation files:

| File | Update Description | Can Parallel |
|------|-------------------|--------------|
| `README.md` (root) | Update plugin description and structure | Yes |
| `docs/concepts.md` | Update concepts documentation with FPF workflow pattern | Yes |
| `docs/guides/decision-making.md` | Create/update hypothesis generation guide | Yes |
| `docs/guides/brainstorming-to-implementation.md` | Update with FPF workflow reference | Yes |
| `docs/resources/related-projects.md` | Update related projects documentation | Yes |
| `docs/reference/commands.md` | Update commands documentation with new FPF commands | Yes |

#### Expected Output

- All documentation files updated with new FPF plugin structure
- Consistent terminology across all docs
- Usage examples where appropriate

#### Verification

**Level:** ✅ Per-Document Judges (6 separate judges in parallel)
**Artifacts:**

- `README.md` (root)
- `docs/concepts.md`
- `docs/guides/decision-making.md`
- `docs/guides/brainstorming-to-implementation.md`
- `docs/resources/related-projects.md`
- `docs/reference/commands.md`

**Threshold:** 4.0/50
**Judge Agent:** `.claude/agents/judge.md`

**Rubric (per document):**

| Criterion | Weight | Description |
|-----------|--------|-------------|
| FPF Reference Added | 0.30 | New FPF plugin mentioned appropriately |
| Consistency | 0.25 | Terminology matches plugin README |
| Integration Quality | 0.25 | Fits naturally with existing content |
| No Redundancy | 0.20 | Doesn't duplicate other docs unnecessarily |

**Reference:** `plugins/fpf/README.md` for terminology consistency

**Execution:** Launch 6 judge agents in parallel (one per document).
**Aggregation:** Report pass/fail for each document. If any fail, list which need revision.

---

### Step 7: Clean Up Old Commands

**Depends on:** Step 4, Step 6a, Step 6b, Step 6c

Delete old q0-q5 commands that are now workflow tasks.

#### Files to Delete

- `q0-init.md` - replaced by init-context.md task
- `q1-hypothesize.md` - replaced by generate-hypotheses.md task
- `q1-add.md` - replaced by add-user-hypothesis.md task
- `q2-verify.md` - replaced by verify-logic.md task
- `q3-validate.md` - replaced by validate-evidence.md task
- `q4-audit.md` - replaced by audit-trust.md task
- `q5-decide.md` - replaced by decide.md task

#### Risks

- Breaking existing users of q0-q5 commands → plugin in development, and not yet used by anyone

#### Verification

**Level:** ❌ NOT NEEDED
**Rationale:** Simple file deletion. Binary success (files deleted or not). File system check sufficient.

---

## Verification Summary

| Step | Verification Level | Judges | Threshold | Artifacts |
|------|-------------------|--------|-----------|-----------|
| 1 | ❌ None | - | - | Directories |
| 2a | ✅ Panel (2) | 2 | 4.0/50 | Agent definition |
| 2b | ✅ Panel (2) | 2 | 4.0/50 | Workflow command |
| 3 | ✅ Per-Item | 5 | 4.0/50 | Utility commands |
| 4 | ✅ Per-Item | 7 | 4.0/50 | Task files |
| 5 | ❌ None | - | - | Plugin manifest |
| 6a | ✅ Panel (2) | 2 | 4.0/50 | Plugin README |
| 6b | ✅ Per-Item | 6 | 4.0/50 | Documentation files |
| 7 | ❌ None | - | - | File deletion |

**Total Judge Invocations:** 24 (4 panels of 2 + 5 + 7 + 6 individual)
**Judge Agent:** `.claude/agents/judge.md`
**Implementation Command:** `.claude/commands/implement-task.md`

---

## Blockers

- None identified - all patterns exist in codebase

## Notes

- The single `propose-hypotheses` workflow now covers the complete FPF cycle from problem framing to decision
- Utility commands (status, query, decay, actualize, reset) remain available for inspection/maintenance
