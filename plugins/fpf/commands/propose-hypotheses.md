---
description: Execute complete FPF cycle from hypothesis generation to decision
argument-hint: "[problem-statement]"
allowed-tools: Task, Read, Write, Bash, AskUserQuestion
---

# Propose Hypotheses Workflow

Execute the First Principles Framework (FPF) cycle: generate competing hypotheses, verify logic, validate evidence, audit trust, and produce a decision.

## User Input

```text
Problem Statement: $ARGUMENTS
```

## Workflow Execution

### Step 1a: Create Directory Structure (Main Agent)

Create `.fpf/` directory structure if it does not exist:

```bash
mkdir -p .fpf/{evidence,decisions,sessions,knowledge/{L0,L1,L2,invalid}}
touch .fpf/{evidence,decisions,sessions,knowledge/{L0,L1,L2,invalid}}/.gitkeep
```

**Postcondition**: `.fpf/` directory scaffold exists.

---

### Step 1b: Initialize Context (FPF Agent)

Launch fpf-agent:
- **Description**: "Initialize FPF context"
- **Prompt**:
  ```
  Read ${CLAUDE_PLUGIN_ROOT}/tasks/init-context.md and execute.

  Problem Statement: $ARGUMENTS
  ```

**Capture**: Context summary from `.fpf/context.md`

---

### Step 2: Generate Hypotheses (FPF Agent)

Launch fpf-agent:
- **Description**: "Generate L0 hypotheses"
- **Prompt**:
  ```
  Read ${CLAUDE_PLUGIN_ROOT}/tasks/generate-hypotheses.md and execute.

  Problem Statement: $ARGUMENTS
  Context: <summary from Step 1b>
  ```

**Capture**: List of hypothesis IDs and titles from `.fpf/knowledge/L0/`

---

### Step 3: Present Summary (Main Agent)

1. Read all L0 hypothesis files from `.fpf/knowledge/L0/`
2. Present summary table:

| ID | Title | Kind | Scope |
|----|-------|------|-------|
| ... | ... | ... | ... |

3. Ask user: "Would you like to add any hypotheses of your own? (yes/no)"

---

### Step 4: Add User Hypothesis (FPF Agent, Conditional Loop)

**Condition**: User says yes to adding hypotheses.

Launch fpf-agent:
- **Description**: "Add user hypothesis"
- **Prompt**:
  ```
  Read ${CLAUDE_PLUGIN_ROOT}/tasks/add-user-hypothesis.md and execute.

  User Hypothesis Description: <get from user>
  ```

**Loop**: Return to Step 3 after hypothesis is added.

**Exit**: When user says no or declines to add more.

---

### Step 5: Verify Logic (Parallel Sub-Agents)

**Condition**: User finished adding hypotheses.

For EACH L0 hypothesis file in `.fpf/knowledge/L0/`, launch parallel agent:
- **Description**: "Verify hypothesis: <hypothesis-id>"
- **Prompt**:
  ```
  Read ${CLAUDE_PLUGIN_ROOT}/tasks/verify-logic.md and execute.

  Hypothesis ID: <hypothesis-id>
  Hypothesis File: .fpf/knowledge/L0/<hypothesis-id>.md
  ```

**Wait for all agents**, then collect results.

**Capture**: For each hypothesis: PASS (promoted to L1) or FAIL (moved to invalid)

---

### Step 6: Validate Evidence (Parallel Sub-Agents)

For EACH L1 hypothesis file in `.fpf/knowledge/L1/`, launch parallel agent:
- **Description**: "Validate hypothesis: <hypothesis-id>"
- **Prompt**:
  ```
  Read ${CLAUDE_PLUGIN_ROOT}/tasks/validate-evidence.md and execute.

  Hypothesis ID: <hypothesis-id>
  Hypothesis File: .fpf/knowledge/L1/<hypothesis-id>.md
  ```

**Wait for all agents**, then collect results.

**Capture**: For each hypothesis: validation result with confidence score

---

### Step 7: Audit Trust (Parallel Sub-Agents)

For EACH L2 hypothesis file in `.fpf/knowledge/L2/`, launch parallel agent:
- **Description**: "Audit trust: <hypothesis-id>"
- **Prompt**:
  ```
  Read ${CLAUDE_PLUGIN_ROOT}/tasks/audit-trust.md and execute.

  Hypothesis ID: <hypothesis-id>
  Hypothesis File: .fpf/knowledge/L2/<hypothesis-id>.md
  ```

**Wait for all agents**, then collect results.

**Capture**: For each hypothesis: R_eff score and weakest link

---

### Step 8: Make Decision (FPF Agent)

Launch fpf-agent:
- **Description**: "Create decision record"
- **Prompt**:
  ```
  Read ${CLAUDE_PLUGIN_ROOT}/tasks/decide.md and execute.

  Problem Statement: $ARGUMENTS
  L2 Hypotheses Directory: .fpf/knowledge/L2/
  ```

**Capture**: DRR file path and recommended hypothesis

---

### Step 9: Present Final Summary (Main Agent)

1. Read the DRR from `.fpf/decisions/`
2. Present results:

**Decision Summary**

| Hypothesis | R_eff | Weakest Link | Status |
|------------|-------|--------------|--------|
| ... | ... | ... | ... |

**Recommended Decision**: <hypothesis title>

**Rationale**: <brief explanation>

3. Present next steps:
   - Implement the selected hypothesis
   - Use `/fpf:status` to check FPF state
   - Use `/fpf:actualize` if codebase changes

---

## Completion

Workflow complete when:
- [ ] `.fpf/` directory structure exists
- [ ] Context recorded in `.fpf/context.md`
- [ ] Hypotheses generated, verified, validated, and audited
- [ ] DRR created in `.fpf/decisions/`
- [ ] Final summary presented to user

**Artifacts Created**:
- `.fpf/context.md` - Problem context
- `.fpf/knowledge/L0/*.md` - Initial hypotheses
- `.fpf/knowledge/L1/*.md` - Verified hypotheses
- `.fpf/knowledge/L2/*.md` - Validated hypotheses
- `.fpf/knowledge/invalid/*.md` - Rejected hypotheses
- `.fpf/evidence/*.md` - Evidence files
- `.fpf/decisions/*.md` - Design Rationale Record
