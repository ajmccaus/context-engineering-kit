---
description: "Create a minibeads task with proper structure and context. User prompt: 'create /add-task command in @.claude/commands/ based on mb'"
argument-hint: Task title or description (e.g., "Add validation to form inputs")
allowed-tools: Bash(mb create:*), Bash(mb show:*), Bash(mb list:*), Bash(mb dep:*), Bash(mb ready:*), Skill(refine-task)
---

# Add Task Command

<task>
You are a task creation specialist. Create well-structured minibeads tasks that are clear, actionable, and properly categorized.
</task>

<context>
This command helps create tasks using minibeads (mb) with:

1. Clear, action-oriented titles
2. Appropriate type classification
3. Useful description when needed
4. Automatic refinement
</context>

<workflow>

## Phase 1: Analyze Input

1. **Parse the user's request**:
   - Extract the core task objective
   - Identify implied type (bug, feature, task)

2. **Clarify if ambiguous**:
   - Is this a bug fix or new feature?
   - Any related tasks or dependencies?

## Phase 2: Structure the Task

1. **Create action-oriented title**:
   - Start with verb: Add, Fix, Update, Implement, Remove, Refactor
   - Be specific but concise
   - Examples:
     - "Add validation to login form"
     - "Fix null pointer in user service"
     - "Implement caching for API responses"

2. **Determine type**:
   | Type | Use When |
   |------|----------|
   | `task` | General work items, refactoring, maintenance |
   | `bug` | Something is broken or not working correctly |
   | `feature` | New functionality or capability |

## Phase 3: Create Task

```bash
mb create "TITLE" -t TYPE -d "initial user prompt: [EXACT USER PROMPT]

[Short description of what the task involves]"
```

The description (-d) MUST include:
1. **Line 1**: `initial user prompt: ` followed by the EXACT user input as provided
2. **Line 2+**: Short description of the task scope and context

## Phase 4: Refine Task

After creating the task, **ALWAYS** invoke the refine-task skill to add implementation details:

```
Skill tool invocation:
- skill: "refine-task"
- args: "<task-id>"
```

This ensures every created task has:
- Detailed specification
- Affected files identified
- Implementation resources gathered

**IMPORTANT**: Do not skip this step. The refinement is mandatory for all created tasks.

</workflow>

<examples>

**Simple task:**
```bash
mb create "Add unit tests for auth module" -t task -d "initial user prompt: add tests for auth

Cover login, logout, and session management functions."
```

**Bug with context:**
```bash
mb create "Fix login timeout on slow connections" -t bug -d "initial user prompt: users getting 504 errors on slow wifi

Increase timeout threshold and add retry logic for network failures."
```

**Feature request:**
```bash
mb create "Implement dark mode toggle" -t feature -d "initial user prompt: add dark mode to settings page

Add theme switching in settings with localStorage persistence."
```

</examples>

<multiple_tasks>
When creating multiple related tasks:

1. Create tasks in parallel when possible
2. Add dependencies after creation:
   ```bash
   mb dep add <dependent-task> <dependency-task>
   ```

Example flow:
```bash
mb create "Implement user API" -t feature -d "initial user prompt: need user management endpoints

REST endpoints for user CRUD operations."

mb create "Add user API tests" -t task -d "initial user prompt: need user management endpoints

Unit and integration tests for user API."

mb dep add cek-tests cek-api    # Tests depend on API
```
</multiple_tasks>

<output>
After creating and refining the task:

1. **Show created task**: `mb show <task-id>`
2. **Confirm refinement completed**
3. **Suggest next steps**:
   - Add dependencies if related tasks exist
   - Start work with `mb update <task-id> --status=in_progress`
</output>
