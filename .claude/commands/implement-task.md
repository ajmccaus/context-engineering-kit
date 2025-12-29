---
description: Implement a task with automated LLM-as-Judge verification for critical steps
argument-hint: Task ID (e.g., cek-31ce) with implementation steps defined
allowed-tools: Task, Read, Write, Bash, Glob, Grep, TodoWrite
---

# Implement Task with Verification

Execute task implementation steps with automated quality verification using LLM-as-Judge for critical artifacts.

## User Input

```text
Task ID: $ARGUMENTS
```

## Overview

This command orchestrates multi-step task implementation with:

1. **Parallel execution** where dependencies allow
2. **Automated verification** using judge agents for critical steps
3. **Panel of LLMs (PoLL)** for high-stakes artifacts
4. **Aggregated voting** with position bias mitigation

## Phase 1: Load and Analyze Task

### Step 1.1: Load Task Details

Read the task file:

```bash
Read .beads/issues/$TASK_ID.md
```

### Step 1.2: Identify Implementation Steps

Parse the `## Implementation Process` section:

- List all steps with dependencies
- Identify which steps have `Parallel with:` annotations
- Classify each step's verification needs from `#### Verification` sections:

| Verification Level | When to Use | Judge Configuration |
|--------------------|-------------|---------------------|
| None | Simple operations (mkdir, delete) | Skip verification |
| Single Judge | Non-critical artifacts | 1 judge, threshold 3.0/5.0 |
| Panel of 2 Judges | Critical artifacts | 2 judges, median voting, threshold 3.5/5.0 |
| Per-Item Judges | Multiple similar items | 1 judge per item, parallel |

### Step 1.3: Create Todo List

Create TodoWrite with all implementation steps, marking verification steps.

## Phase 2: Execute Implementation Steps

For each step in dependency order:

### Pattern A: Simple Step (No Verification)

**1. Launch Implementation Agent:**

```
Implement Step [N]: [Step Title]

Read .beads/issues/$TASK_ID.md

Your task:
- Execute ONLY Step [N]: [Step Title]
- Do NOT execute any other steps
- Follow the Expected Output and Success Criteria exactly

Context:
- Task: [Task title from file]
- Step description: [Copy from task file]
- Expected output: [Copy from task file]
- Success criteria: [Copy from task file]

When complete, report what was created/modified.
```

**2. Verify Completion:**

- Check file exists / operation completed
- Basic validation (valid JSON, file not empty, etc.)

**3. Mark Step Complete**

---

### Pattern B: Critical Step (Panel of 2 Judges)

**1. Launch Implementation Agent:**

```
Implement Step [N]: [Step Title]

Read .beads/issues/$TASK_ID.md

Your task:
- Execute ONLY Step [N]: [Step Title]
- Do NOT execute any other steps
- Follow the Expected Output and Success Criteria exactly

Context:
- Task: [Task title from file]
- Step description: [Copy from task file]
- Expected output: [Copy from task file]
- Success criteria: [Copy from task file]

Reference patterns (if applicable):
- [Path to reference file for this artifact type]

Constraints:
- Implement exactly what is specified, no more, no less
- Follow existing patterns in the codebase

When complete, report what was created/modified.
```

**2. Wait for Completion**

**3. Launch 2 Judge Agents in Parallel:**

**Judge 1:**

```
Read .claude/agents/judge.md

Evaluate artifact at: [artifact_path]

**Chain-of-Thought Requirement:** Justification MUST be provided BEFORE score for each criterion.

Use this rubric:
[paste rubric from #### Verification section in task file]

Context:
- This is Step [N]: [Step Title]
- Passing threshold: [threshold from task file]
- Reference pattern: [if applicable]

Return structured evaluation report with:
- Score per criterion with evidence
- Overall weighted score
- PASS/FAIL determination
- Improvement suggestions if FAIL
```

**Judge 2:**

```
Read .claude/agents/judge.md

Evaluate artifact at: [artifact_path]

**Chain-of-Thought Requirement:** Justification MUST be provided BEFORE score for each criterion.

Use this rubric:
[paste rubric from #### Verification section in task file]

Context:
- This is Step [N]: [Step Title]
- Passing threshold: [threshold from task file]
- Reference pattern: [if applicable]

Return structured evaluation report with:
- Score per criterion with evidence
- Overall weighted score
- PASS/FAIL determination
- Improvement suggestions if FAIL

```

**4. Aggregate Results:**

- Calculate median score per criterion

- Flag high-variance criteria (std > 1.0)
- Pass if median overall ≥ threshold

**5. If FAIL:**

- Present issues to user
- Ask: "Should I attempt to fix these issues?"
- If yes, re-implement and re-verify

---

### Pattern C: Multi-Item Step (Per-Item Judges)

For steps that create multiple similar items:

**1. Launch Implementation Agents in Parallel (one per item):**

For each item in the step's item list:

```
Implement Step [N], Item: [Item Name]

Read .beads/issues/$TASK_ID.md

Your task:
- Create ONLY [item_name] from Step [N]
- Do NOT create other items or steps
- Follow the Expected Output and Success Criteria exactly

Context:
- Task: [Task title]
- Item: [Item name and description from task file]
- Expected output for this item: [From task file]
- Success criteria: [From task file]

Reference patterns (if applicable):
- [Path to reference file for this item type]

When complete, report what was created.
```

**2. Wait for All Completions**

**3. Launch Judge Agents in Parallel (one per item):**

For each item:

```
Read .claude/agents/judge.md

Evaluate artifact at: [item_path]

**Chain-of-Thought Requirement:** Justification MUST be provided BEFORE score for each criterion.

Use this rubric:
[paste item-specific rubric from #### Verification section]

Context:
- This is Step [N]: [Step Title], Item: [Item Name]
- Passing threshold: [threshold]

Return structured evaluation report.

```

**4. Collect All Results**

**5. Report Aggregate:**

- Items passed: X/Y
- Items needing revision: [list with specific issues]

**6. If Any FAIL:**

- Present failing items with judge feedback
- Ask: "Should I fix failing items?"
- If yes, re-implement only failing items and re-verify

---

## Phase 3: Verification Specifications

Task files define verification in `#### Verification` sections with:

### Required Elements

1. **Level**: None / Single Judge / Panel (2) / Per-Item
2. **Artifact(s)**: Path(s) to file(s) being verified
3. **Threshold**: Passing score (typically 3.0/5.0 or 3.5/5.0)
4. **Rubric**: Table with criteria, weights, and descriptions

### Rubric Format

```markdown
| Criterion | Weight | Description |
|-----------|--------|-------------|
| [Name 1]  | 0.XX   | [What to evaluate] |
| [Name 2]  | 0.XX   | [What to evaluate] |
| ...       | ...    | ...         |

```

Weights MUST sum to 1.0.

### Scoring Scale

For each criterion:

- **1 (Poor)**: Does not meet requirements
- **3 (Adequate)**: Meets basic requirements
- **5 (Excellent)**: Exceeds requirements

### Judge Prompt Construction

Extract from task file's `#### Verification` section:

1. Rubric table → Convert to judge prompt format
2. Threshold → Include in context
3. Artifact path → Include in prompt
4. Reference patterns → Include if specified

### Chain-of-Thought Enforcement

Every judge prompt MUST include:

```
**Chain-of-Thought Requirement:** Justification MUST be provided BEFORE score for each criterion.
```

This improves evaluation reliability by 15-25%.

---

## Phase 4: Aggregation and Reporting

### Panel Voting Algorithm

When using 2+ judges, follow these manual computation steps:

- Think in steps, output each step result separately!
- Do not skip steps!

#### Step 1: Collect Scores per Criterion

Create a table with each criterion and scores from all judges:

| Criterion | Judge 1 | Judge 2 | Median | Difference |
|-----------|---------|---------|--------|------------|
| [Name 1]  | X.X     | X.X     | ?      | ?          |
| [Name 2]  | X.X     | X.X     | ?      | ?          |

#### Step 2: Calculate Median for Each Criterion

For 2 judges: **Median = (Score1 + Score2) / 2**

For 3+ judges: Sort scores, take middle value (or average of two middle values if even count)

#### Step 3: Check for High Variance

**High variance** = judges disagree significantly (difference > 2.0 points)

Formula: `|Judge1 - Judge2| > 2.0` → Flag as high variance

#### Step 4: Calculate Weighted Overall Score

Multiply each criterion's median by its weight and sum:

```
Overall = (Criterion1_Median × Weight1) + (Criterion2_Median × Weight2) + ...
```

#### Step 5: Determine Pass/Fail

Compare overall score to threshold:

- `Overall ≥ Threshold` → **PASS**
- `Overall < Threshold` → **FAIL**

---

#### Worked Example

**Scenario**: 2 judges evaluated an artifact with threshold 4.0/5.0

**Rubric**:

| Criterion | Weight |
|-----------|--------|
| Clarity | 0.30 |
| Completeness | 0.40 |
| Correctness | 0.30 |

**Judge Scores**:

| Criterion | Judge 1 | Judge 2 |
|-----------|---------|---------|
| Clarity | 4.0 | 4.5 |
| Completeness | 3.0 | 3.5 |
| Correctness | 4.0 | 3.0 |

**Step 2 - Calculate Medians**:

- Clarity: (4.0 + 4.5) / 2 = **4.25**
- Completeness: (3.0 + 3.5) / 2 = **3.25**
- Correctness: (4.0 + 3.0) / 2 = **3.5**

**Step 3 - Check High Variance**:

- Clarity: |4.0 - 4.5| = 0.5 → OK
- Completeness: |3.0 - 3.5| = 0.5 → OK
- Correctness: |4.0 - 3.0| = 1.0 → OK (borderline, but < 2.0)

No high variance criteria.

**Step 4 - Calculate Overall**:

```
Overall = (4.25 × 0.30) + (3.25 × 0.40) + (3.5 × 0.30)
        = 1.275 + 1.30 + 1.05
        = 3.625
```

**Step 5 - Determine Result**:

- Threshold: 4.0
- Overall: 3.625
- 3.625 < 4.0 → **FAIL ❌**

---

#### Quick Reference Formulas

| Calculation | Formula |
|-------------|---------|
| Median (2 judges) | (J1 + J2) / 2 |
| Median (3 judges) | Middle value when sorted |
| High variance check | \|J1 - J2\| > 2.0 |
| Weighted score | Σ(Median × Weight) |
| Pass condition | Overall ≥ Threshold |

### Handling Disagreement

If judges significantly disagree (std > 1.5 on any criterion):

1. Flag the criterion
2. Present both judges' reasoning
3. Ask user: "Judges disagree on [criterion]. Review manually?"
4. If yes: present evidence, get user decision
5. If no: use median (conservative approach)

### Final Report

After all steps complete:

```markdown
## Implementation Summary

### Steps Completed
| Step | Status | Verification | Score |
|------|--------|--------------|-------|
| 1    | ✅     | Skipped      | N/A   |
| 2a   | ✅     | Panel (2)    | 4.2/5 |
| 2b   | ✅     | Panel (2)    | 3.8/5 |
| ...  | ...    | ...          | ...   |

### Verification Summary
- Total artifacts verified: X
- Passed on first try: Y
- Required revision: Z
- Final pass rate: W%

### High-Variance Criteria (Judges Disagreed)
- [Criterion] in [Step]: Judge 1 scored X, Judge 2 scored Y

### Recommendations
1. [Any follow-up actions]
2. [Suggested improvements]
```

---

## Execution Flow

```
┌──────────────────────────────────────────────────────────────┐
│                    IMPLEMENT TASK WITH VERIFICATION           │
├──────────────────────────────────────────────────────────────┤
│                                                                │
│  Phase 1: Load Task                                           │
│  ┌─────────────────────────────────────────────────────────┐ │
│  │ mb show $TASK_ID → Read task file → Parse steps         │ │
│  │ → Extract #### Verification specs → Create TodoWrite    │ │
│  └─────────────────────────────────────────────────────────┘ │
│                           │                                   │
│                           ▼                                   │
│  Phase 2: Execute Steps (Respecting Dependencies)             │
│  ┌─────────────────────────────────────────────────────────┐ │
│  │                                                           │ │
│  │  For each step (parallel where allowed):                 │ │
│  │                                                           │ │
│  │  ┌─────────────┐    ┌──────────────┐    ┌────────────┐  │ │
│  │  │ Implement   │───▶│ Verify?      │───▶│ Pass?      │  │ │
│  │  │ (sub-agent) │    │ (judge(s))   │    │            │  │ │
│  │  └─────────────┘    └──────────────┘    └────────────┘  │ │
│  │                                               │    │     │ │
│  │                                              Yes   No    │ │
│  │                                               │    │     │ │
│  │                                               ▼    ▼     │ │
│  │                                          Continue  Fix   │ │
│  │                                                    ↺     │ │
│  └─────────────────────────────────────────────────────────┘ │
│                           │                                   │
│                           ▼                                   │
│  Phase 4: Aggregate & Report                                  │
│  ┌─────────────────────────────────────────────────────────┐ │
│  │ Collect all verification results                         │ │
│  │ → Calculate aggregate metrics                            │ │
│  │ → Generate final report                                  │ │
│  │ → Present to user                                        │ │
│  └─────────────────────────────────────────────────────────┘ │
│                                                                │
└──────────────────────────────────────────────────────────────┘
```

---

## Usage Examples

### Example 1: Implementing a Plugin Reorganization

```
User: /implement-task cek-31ce

Phase 1: Loading task...
Task: "Reorganize FPF plugin using workflow command pattern"
Steps identified: 7 main steps

Verification plan (from #### Verification sections):
- Step 1: No verification (directory creation)
- Step 2a: Panel of 2 judges (agent definition)
- Step 2b: Panel of 2 judges (workflow command)
- Step 3: 5 separate judges (utility commands)
- Step 4: 7 separate judges (task files)
- Step 5: No verification (plugin manifest)
- Step 6a: Panel of 2 judges (plugin README)
- Step 6b: 6 separate judges (documentation files)
- Step 7: No verification (file deletion)

Phase 2: Executing...

Step 1: Launching implementation agent...
  Agent prompt: "Implement Step 1: Create Directory Structure..."
  Result: ✅ Directories created

Step 2a, 2b, 3: Launching parallel implementation agents...
  [3 agents working simultaneously]

Step 2a Implementation complete. Launching verification...
  Judge 1 prompt: "Evaluate artifact at: plugins/fpf/agents/fpf-agent.md..."
  Judge 2 prompt: "Evaluate artifact at: plugins/fpf/agents/fpf-agent.md..."
  
Step 2a Verification:
  Judge 1: 4.1/5.0 - PASS
  Judge 2: 4.3/5.0 - PASS
  Panel Result: 4.2/5.0 ✅

[Continue for all steps...]

Phase 4: Final Report
Implementation complete.
- 7/7 steps completed
- 20 artifacts verified
- 19 passed first try
- 1 required revision (task file)
- Final pass rate: 100%
```

### Example 2: Handling Verification Failure

```
Step 4 Implementation (generate-hypotheses.md) complete.
Launching verification...

Judge prompt: "Evaluate artifact at: plugins/fpf/tasks/generate-hypotheses.md
**Chain-of-Thought Requirement:** Justification MUST be provided BEFORE score..."

Judge result: 2.8/5.0 - FAIL

Issues found:
- Self-Containment: 2/5
  Evidence: "File references `.fpf/context.md` without providing context"
  Justification: "Sub-agent would need to read external file to understand task"
- Success Criteria: 2/5
  Evidence: "No checkboxes found in Success Criteria section"
  Justification: "Criteria listed as prose, not actionable checkboxes"

Should I attempt to fix these issues? [Y/n]

User: Y

Launching fix agent...
Agent prompt: "Fix Step 4 item: generate-hypotheses.md
Issues to address:
1. Self-Containment: Add context from .fpf/context.md inline
2. Success Criteria: Convert to checkbox format..."

Re-verification:
  Judge: 4.0/5.0 - PASS ✅
```

---

## Checklist

Before completing implementation:

- [ ] All steps executed in dependency order
- [ ] Parallel steps launched simultaneously (not sequentially)
- [ ] Each implementation agent received focused prompt with exact step
- [ ] All critical artifacts verified by judges
- [ ] Panel voting used for high-stakes artifacts
- [ ] Chain-of-thought requirement included in all judge prompts
- [ ] Failed verifications addressed
- [ ] Final report generated
- [ ] User informed of any judge disagreements
