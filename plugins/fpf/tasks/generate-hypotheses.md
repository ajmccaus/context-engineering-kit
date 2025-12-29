# Step 2: Generate Hypotheses

## Context

You are executing step 2 of the `propose-hypotheses` workflow. The bounded context has been established in `.fpf/context.md`. Your task is to generate diverse L0 (conjecture) hypotheses using abductive reasoning.

You are an FPF Reasoning Specialist operating within the **Abduction** phase of the ADI cycle. This is the creative phase where you generate plausible explanations for the observed anomaly or problem.

## Goal

Generate 3-5 diverse L0 hypotheses that address the user's problem or question. Each hypothesis MUST be written as a separate file in `.fpf/knowledge/L0/`. The hypotheses should span a spectrum from conservative (low-risk, incremental) to radical (high-innovation, transformative).

## Input

- **Problem/Question**: Provided by the orchestrator as context
- **Bounded Context**: Read from `.fpf/context.md` (contains vocabulary, invariants, constraints)

## Instructions

### 1. Read the Bounded Context

```bash
# MUST read context before generating hypotheses
cat .fpf/context.md
```

Extract:
- Problem statement and scope
- Domain vocabulary and terminology
- Invariants and constraints
- Key assumptions

### 2. Frame the Anomaly

Identify the core anomaly or question that needs explanation:
- What is the unexpected observation or challenge?
- What assumptions does it challenge?
- What gap in knowledge does it reveal?

### 3. Generate Diverse Hypotheses

Create 3-5 hypotheses following this diversity spectrum:

| Type | Description | Risk Profile | Innovation |
|------|-------------|--------------|------------|
| **Conservative** | Uses proven patterns, minimal change | Low | Incremental |
| **Moderate** | Balances innovation with stability | Medium | Evolutionary |
| **Radical** | Challenges assumptions, high novelty | High | Transformative |

**MUST generate at least:**
- 1 conservative hypothesis
- 1-2 moderate hypotheses
- 1 radical hypothesis

### 4. Create Hypothesis Files

For EACH hypothesis, create a file in `.fpf/knowledge/L0/` with kebab-case naming:

**File naming**: `<hypothesis-id>.md` (e.g., `use-redis-for-caching.md`)

**Required frontmatter fields:**

```yaml
---
id: <kebab-case-unique-identifier>
title: <Human Readable Title>
kind: system | episteme
scope: <Where this applies, constraints, requirements>
decision_context: <parent-decision-id>
depends_on: []
created: <ISO 8601 timestamp>
layer: L0
---
```

**Required sections in body:**

```markdown
# <Title>

## Method (The Recipe)

Detailed description of HOW this hypothesis works:
1. Step-by-step implementation approach
2. Technical details or process changes
3. Integration points

## Expected Outcome

What success looks like when this hypothesis is validated and implemented:
- Measurable benefits
- Observable changes
- Success metrics

## Rationale

Why this approach was generated:
- **Anomaly**: What problem this addresses
- **Approach**: Why this solution fits the context
- **Assumptions**: What must hold true for this to work
- **Risk Level**: Conservative | Moderate | Radical
```

### 5. Field Reference

| Field | Required | Valid Values | Description |
|-------|----------|--------------|-------------|
| `id` | Yes | kebab-case | Unique identifier, matches filename |
| `title` | Yes | string | Human-readable hypothesis name |
| `kind` | Yes | `system`, `episteme` | `system` = code/architecture; `episteme` = process/documentation |
| `scope` | Yes | string | Applicability, constraints, requirements |
| `decision_context` | Yes | kebab-case | Groups related hypotheses for same decision |
| `depends_on` | No | list | IDs of prerequisite hypotheses |
| `created` | Yes | ISO 8601 | Timestamp when created |
| `layer` | Yes | `L0` | Always `L0` for new hypotheses |

### 6. Quality Checklist for Each Hypothesis

Before creating each file, verify:
- [ ] Hypothesis is falsifiable (can be proven wrong)
- [ ] Scope is clearly bounded (not too vague or too narrow)
- [ ] Method is actionable (clear steps to implement)
- [ ] Rationale explains why this fits the context
- [ ] Risk level is explicitly stated

## Constraints

- **MUST** create actual files in `.fpf/knowledge/L0/` - mentioning hypotheses in prose does NOT create them
- **MUST NOT** skip the conservative or radical ends of the spectrum
- **MUST NOT** generate more than 5 hypotheses (overwhelms decision-making)
- **MUST NOT** generate fewer than 3 hypotheses (insufficient diversity)
- **MUST NOT** proceed without reading `.fpf/context.md` first
- **MUST** use `kind: system` for code/architecture changes, `kind: episteme` for process/documentation changes
- **MUST** use same `decision_context` value for all hypotheses in this batch

## Expected Output

Return a structured summary for the orchestrator:

```markdown
## Task Result

**Status**: SUCCESS | FAILURE | BLOCKED
**Files Created**:
- `.fpf/knowledge/L0/<hypothesis-1>.md`
- `.fpf/knowledge/L0/<hypothesis-2>.md`
- `.fpf/knowledge/L0/<hypothesis-3>.md`
- [additional files if 4-5 hypotheses]

## Hypothesis Summary

| ID | Title | Kind | Risk Level |
|----|-------|------|------------|
| <id-1> | <title-1> | system/episteme | Conservative |
| <id-2> | <title-2> | system/episteme | Moderate |
| <id-3> | <title-3> | system/episteme | Radical |

## Decision Context

**Context ID**: <decision-context-id>
**Problem Addressed**: <brief problem statement>
**Spectrum Coverage**: Conservative (N) | Moderate (N) | Radical (N)

## Next Steps

The orchestrator should:
1. Present hypothesis summary to user
2. Ask if user wants to add their own hypotheses
3. Proceed to verification phase when hypothesis set is complete
```

## Success Criteria

- [ ] Read `.fpf/context.md` before generating hypotheses
- [ ] Created 3-5 L0 hypothesis files in `.fpf/knowledge/L0/`
- [ ] All files have valid YAML frontmatter with required fields
- [ ] At least one conservative hypothesis generated
- [ ] At least one radical hypothesis generated
- [ ] All hypotheses share the same `decision_context` value
- [ ] Each hypothesis has Method, Expected Outcome, and Rationale sections
- [ ] Returned structured summary suitable for orchestrator consumption

## Example Hypothesis File

For reference, here is a complete example:

```markdown
---
id: use-redis-for-caching
title: Use Redis for Distributed Caching
kind: system
scope: High-load API endpoints, requires Redis infrastructure, Linux environments
decision_context: caching-strategy-decision
depends_on: []
created: 2025-01-15T10:30:00Z
layer: L0
---

# Use Redis for Distributed Caching

## Method (The Recipe)

1. Deploy Redis cluster with 3 nodes for high availability
2. Implement cache-aside pattern in API service layer
3. Configure TTL-based expiration for each data type:
   - User sessions: 30 minutes
   - API responses: 5 minutes
   - Static data: 1 hour
4. Add cache invalidation hooks to write operations
5. Implement circuit breaker for Redis connection failures

## Expected Outcome

- API response times reduced by 60-80% for cached endpoints
- Database load reduced by 40% during peak traffic
- Horizontal scalability achieved through shared cache state
- Graceful degradation when cache unavailable

## Rationale

- **Anomaly**: API latency spikes during peak hours due to repeated database queries
- **Approach**: Redis provides proven, battle-tested distributed caching with sub-millisecond latency
- **Assumptions**: Infrastructure team can provision Redis; data is cacheable (eventually consistent acceptable)
- **Risk Level**: Conservative - Redis is industry standard with well-understood trade-offs
```
