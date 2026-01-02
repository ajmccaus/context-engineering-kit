# FPF - First Principles Framework

Implementation of structured reasoning using the First Principles Framework (FPF) methodology developed by Anatoly Levenchuk. Based on [quint-code](https://github.com/m0n0x41d/quint-code) by m0n0x41d.

> **Warning:** This plugin loads core part of [FPF specification](https://github.com/ailev/FPF) into context, which is huge. Total size of this part is more than 600k tokens. As a result it will be loaded into context of subagent with Sonnet[1m] model. This can eat up your token limit very quickly.

## Overview

FPF addresses a fundamental challenge in AI-assisted development: making decision-making processes transparent and auditable. Rather than having AI jump to solutions, FPF enforces generating competing hypotheses, checking them logically, testing against evidence, then letting developers choose the path forward.

### Key Features

- **Decision Preservation** - All decisions stored in `.fpf/` directory for audit
- **Structured Reasoning** - ADI cycle (Abduction-Deduction-Induction)
- **Trust Calculus** - Computed reliability scores, not estimates
- **Transformer Mandate** - AI generates options; humans decide


## Commands

| Command | Description |
|---------|-------------|
| `/fpf:propose-hypotheses` | Execute complete FPF cycle from hypothesis to decision |
| `/fpf:status` | Show current FPF phase and hypothesis counts |
| `/fpf:query` | Search knowledge base with assurance info |
| `/fpf:decay` | Manage evidence freshness (refresh/deprecate/waive) |
| `/fpf:actualize` | Reconcile knowledge with codebase changes |
| `/fpf:reset` | Archive session and return to IDLE |

### /fpf:query

#### Examples

**Search by keyword:**
```markdown
User: /fpf:query caching

Results:
| Hypothesis | Layer | R_eff |
|------------|-------|-------|
| redis-caching | L2 | 0.85 |
| cdn-edge-cache | L2 | 0.72 |
| lru-cache | invalid | N/A |
```

**Query specific hypothesis:**
```markdown
User: /fpf:query redis-caching

# redis-caching (L2)

Title: Use Redis for Caching
Kind: system
Scope: High-load systems
R_eff: 0.85
Evidence: 2 files
```

**Query decisions:**
```markdown
User: /fpf:query DRR

# Design Rationale Records

| DRR | Date | Winner | Rejected |
|-----|------|--------|----------|
| DRR-2025-01-15-caching | 2025-01-15 | redis-caching | cdn-edge |
```

### /fpf:status

#### Example Output

```markdown
## FPF Status

### Current Phase: DEDUCTION

You have 3 hypotheses in L0 awaiting verification.
Next step: Continue the FPF workflow to process L0 hypotheses.

### Hypothesis Counts

| Layer | Count |
|-------|-------|
| L0 | 3 |
| L1 | 0 |
| L2 | 0 |
| Invalid | 0 |

### Evidence Status

No evidence files yet (hypotheses not validated).

### No Warnings

All systems nominal.
```


### /fpf:reset

#### Example

```markdown
User: /fpf:reset

Agent: What type of reset would you like?

1. **Soft Reset** - Archive current session, start fresh
2. **Hard Reset** - Delete all FPF data (cannot be undone)
3. **Decision Reset** - Keep hypotheses, re-evaluate from earlier phase

User: Soft reset please

Agent: Creating session archive...
       [Creates .fpf/sessions/session-2025-01-15-reset.md]

       Session archived. Knowledge directories cleared.

       Ready for new reasoning cycle.
       Next step: Run `/fpf:propose-hypotheses` to start.
```

#### When to Reset

| Scenario | Recommended Action |
|----------|-------------------|
| Starting a new problem | Soft reset (archive) |
| Wrong direction, start over | Soft reset |
| Testing/learning FPF | Hard reset |
| Re-evaluate with new info | Decision reset |
| Context changed significantly | Soft reset + update context |

## Agent

### fpf-agent

FPF reasoning specialist for hypothesis generation, verification, validation, and trust calculus using the ADI cycle and knowledge layer progression.

**Purpose**: Executes FPF reasoning tasks with file operations for persisting knowledge state.

**Tools**: Read, Write, Glob, Grep, Bash (mkdir, mv, touch)

**Responsibilities**:
- Create hypothesis files in knowledge layers
- Move files between L0/L1/L2/invalid directories
- Create evidence files and audit reports
- Generate Design Rationale Records (DRRs)

## Workflow: propose-hypotheses

The `/fpf:propose-hypotheses` command executes the complete FPF cycle:

### Workflow Steps

| Step | Phase | Description |
|------|-------|-------------|
| 1 | Setup | Initialize context and `.fpf/` directory structure |
| 2 | Abduction | Generate L0 hypotheses for the problem |
| 3 | User Input | Present summary and allow user to add hypotheses |
| 4 | Deduction | Verify logic for all hypotheses (parallel) |
| 5 | Induction | Validate evidence for verified hypotheses (parallel) |
| 6 | Audit | Compute R_eff trust scores (parallel) |
| 7 | Decision | Create DRR with user approval |
| 8 | Summary | Present final results and next steps |

### Detailed Workflow

1. **Initialize Context** - Create `.fpf/` directory structure and capture problem context
2. **Generate Hypotheses** - FPF agent generates 3-5 competing L0 hypotheses
3. **Present Summary + User Loop** - Show hypothesis table, allow user additions
4. **Verify Logic (Parallel)** - Check each hypothesis against constraints, promote to L1 or invalidate
5. **Validate Evidence (Parallel)** - Gather empirical evidence, promote L1 to L2
6. **Audit Trust (Parallel)** - Compute R_eff using Weakest Link principle
7. **Make Decision** - Present comparison table, user selects winner, create DRR
8. **Present Final Summary** - Show DRR, winner rationale, and next steps

## Key Concepts

| Concept | Description |
|---------|-------------|
| **ADI Cycle** | Abduction-Deduction-Induction reasoning loop |
| **Knowledge Layers** | L0 (Conjecture) -> L1 (Substantiated) -> L2 (Corroborated) |
| **WLNK** | Weakest Link principle: R_eff = min(evidence_scores) |
| **Holon** | Knowledge unit with identity, layer, kind, and assurance scores |
| **DRR** | Design Rationale Record documenting decisions |
| **Transformer Mandate** | AI generates options; humans decide |

### Knowledge Layers (Epistemic Status)

| Layer | Name | Meaning | How to reach |
|-------|------|---------|--------------|
| **L0** | Conjecture | Unverified hypothesis | Generate hypotheses |
| **L1** | Substantiated | Passed logical check | Verify logic |
| **L2** | Corroborated | Empirically validated | Validate evidence |
| **Invalid** | Falsified | Failed verification | FAIL verdict |

### Congruence Levels

| Level | Context Match | Penalty |
|-------|--------------|---------|
| CL3 | Same (internal test) | None |
| CL2 | Similar (related project) | Minor |
| CL1 | Different (external docs) | Significant |

## Example Usage

### Basic Decision Flow

```
/fpf:propose-hypotheses What caching strategy should we use?
```

This will:
1. Initialize FPF context for caching strategy decision
2. Generate hypotheses (e.g., Redis, Memcached, in-memory)
3. Allow you to add your own alternatives
4. Verify each against project constraints
5. Gather evidence through tests/research
6. Compute trust scores
7. Present comparison for your decision

### CI/CD Strategy Example

```
User: /fpf:propose-hypotheses How should we deploy our application?

-> Generated hypotheses:
   - H1: GitHub Actions + ECS
   - H2: Docker Swarm + ECR
   - H3: Kamal deployment

-> Verification results:
   - H1: PASS (meets constraints)
   - H2: PASS (meets constraints)
   - H3: FAIL (requires Ruby runtime)

-> Validation results:
   - H1: R=0.75 (external docs, CL1)
   - H2: R=0.85 (internal test)

-> Audit summary:
   | Hypothesis | R_eff | Weakest Link |
   |------------|-------|--------------|
   | ECS | 0.75 | external docs |
   | Docker Swarm | 0.85 | internal test |

-> User selects: Docker Swarm + ECR
-> DRR created with full audit trail
```

## Evidence Freshness

Evidence expires. A benchmark from 6 months ago may not reflect current performance.

### Managing Stale Evidence

```
/fpf:decay                    # Check what's stale

Three options:
- Refresh: Re-run tests for fresh evidence
- Deprecate: Downgrade hypothesis if decision needs rethinking
- Waive: Accept risk temporarily with documented rationale
```

### Natural Language Usage

```
User: Waive the benchmark until February, we'll re-run after migration.

Agent: Waiver recorded:
       - Evidence: ev-benchmark-2024-06-15
       - Until: 2025-02-01
       - Rationale: Will re-run after migration
```

## The Transformer Mandate

A core FPF principle: **A system cannot transform itself.**

- AI generates options with evidence
- Human decides
- Making architectural choices autonomously is a PROTOCOL VIOLATION

This ensures accountability and prevents AI from making unsupervised decisions.

## Directory Structure

The FPF plugin creates and manages this directory structure:

```
.fpf/
├── context.md              # Problem context and constraints
├── knowledge/
│   ├── L0/                 # Candidate hypotheses
│   ├── L1/                 # Substantiated hypotheses
│   ├── L2/                 # Validated hypotheses
│   └── invalid/            # Rejected hypotheses
├── evidence/               # Evidence files and audit reports
├── decisions/              # DRR files
└── sessions/               # Archived sessions
```

## When to Use FPF

**Use it for:**
- Architectural decisions with long-term consequences
- Multiple viable approaches requiring systematic evaluation
- Decisions needing auditable reasoning trails
- Building project knowledge over time

**Skip it for:**
- Quick fixes with obvious solutions
- Easily reversible decisions
- Time-critical situations

## Related Resources

- [FPF Repository](https://github.com/ailev/FPF) - Original methodology by Anatoly Levenchuk
- [quint-code](https://github.com/m0n0x41d/quint-code) - Implementation this plugin is based on
