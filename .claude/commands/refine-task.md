---
description: Refine a minibeads task with detailed specification, affected files, and implementation resources
argument-hint: Task ID (e.g., cek-673b) or 'latest' for most recent task
---

# Refine Task Command

<task>
You are a task refinement specialist. Analyze and enhance minibeads tasks with comprehensive implementation details that enable developers to start work immediately with full context.
</task>

<context>
This command transforms vague or incomplete tasks into actionable specifications by:

1. Analyzing and clarifying the task requirements
2. Writing a detailed implementation specification
3. Identifying files that will be affected
4. Listing useful resources for implementation context
</context>

<workflow>

## Phase 1: Load and Understand Task

1. **Get task details**:
   ```bash
   mb show $TASK_ID
   ```

2. **Understand the current state**:
   - What is the task about?
   - What details are missing?
   - What assumptions need clarification?

3. **Ask clarifying questions** if needed:
   - What is the expected behavior?
   - What triggers this functionality?
   - What are the success criteria?

## Phase 2: Analyze Codebase Impact

1. **Search for related code**:
   - Use Glob to find relevant plugin/module directories
   - Use Grep to find related patterns, keywords, similar implementations
   - Read existing files to understand current structure

2. **Identify files to be modified/created**:
   - **Primary changes**: Core files that implement the feature
   - **Configuration changes**: Manifests, settings, config files
   - **Documentation updates**: READMEs, guides, examples

3. **Find similar implementations**:
   - Look for existing patterns in the codebase
   - Check how similar features were implemented
   - Note reusable patterns and conventions

## Phase 3: Gather Implementation Resources

Collect files useful for building context:

1. **Pattern references**:
   - Similar commands/features in the codebase
   - Related plugin implementations
   - Existing hooks, agents, or workflows

2. **Documentation**:
   - Relevant guides in `docs/guides/`
   - Plugin documentation in `docs/plugins/`
   - Contributing guidelines

3. **External resources**:
   - Official documentation URLs
   - API references
   - Related specifications

## Phase 4: Update Task

Use `mb update` to add comprehensive details:

```bash
mb update $TASK_ID -d "DETAILED_DESCRIPTION"
```

</workflow>

<description_template>

Structure the task description with these sections:

```markdown
[Brief summary of what needs to be implemented]

**Acceptance Criteria:**
- Requirement 1
- Requirement 2
- Requirement 3

**Example Flow:** (if applicable)
1. Step 1
2. Step 2
3. Expected outcome

---

## Files to be Modified/Created

### Primary Changes:
- `path/to/file.ext` - Description of changes
- `path/to/new-file.ext` - NEW: Description

### Configuration Changes:
- `path/to/config.json` - What to add/modify

### Documentation Updates:
- `path/to/README.md` - Document the feature
- `docs/path/to/guide.md` - Update guide

---

## Useful Resources for Implementation

### Pattern References:
- `path/to/similar/implementation.md` - Why useful
- `path/to/example/code.ts` - Pattern to follow

### Documentation:
- `docs/guides/relevant-guide.md` - Key concepts
- Official docs: https://example.com/docs

### Related Code:
- `path/to/related/module/` - Context for integration
```

</description_template>

<checklist>
Before completing refinement:

- [ ] Task has clear, actionable description
- [ ] Implementation requirements are specific
- [ ] All affected files are identified with descriptions
- [ ] Useful resources are listed with context on why they're helpful
- [ ] Similar patterns in codebase have been identified
- [ ] Success criteria are clear
- [ ] No ambiguous requirements remain
</checklist>

<example_session>
User: /refine-task cek-673b

**Phase 1: Loading task...**
```bash
mb show cek-673b
```
Task: "Add hooks to reflect plugin"

**Phase 2: Analyzing codebase impact...**

*Searching for reflexion plugin structure...*
- Found: `plugins/reflexion/` with commands, plugin.json

*Searching for hook implementation examples...*
- Found: `plugins/customaize-agent/commands/create-hook.md`
- Found: `plugins/mcp/commands/setup-codemap-cli.md` with hooks config

*Identifying affected files...*
- Primary: Plugin manifest, new hook script
- Config: Settings.json for hook registration
- Docs: README updates

**Phase 3: Gathering resources...**

*Pattern references:*
- Hook creation guide in customaize-agent
- Real hook example in MCP plugin

*Documentation:*
- Custom extensions guide
- Official Claude Code hooks docs

**Phase 4: Updating task...**

```bash
mb update cek-673b -d "[comprehensive description with all sections]"
```

Task refined with:
- Detailed specification
- 5 files identified (3 to create, 2 to update)
- 8 useful resources listed
</example_session>

<output>
After refinement, report:

1. **Task Updated**: $TASK_ID
2. **Files Identified**: X files to modify/create
3. **Resources Gathered**: X useful references
4. **Ready for Implementation**: Yes/No (any blockers?)

Suggest: `mb show $TASK_ID` to review the refined task
</output>
