# Spec-Driven Development (SDD)

Structured workflow for complex features requiring planning, specifications, and architecture decisions before implementation.

For simple features, use [Feature Development with Quality Gates](./feature-development.md) workflow.

## When to Use

- Features requiring complex development
- Significant architectural changes or integrations

## Plugins needed for this workflow

- [SDD](../plugins/sdd/README.md)
- [Code Review](../plugins/code-review/README.md)
- [Git](../plugins/git/README.md)

## Workflow

### How It Works

```md
┌─────────────────────────────────────────────┐
│ 1. Setup Project Standards                  │
│    (create specs/constitution.md)           │
└────────────────────┬────────────────────────┘
                     │
                     │ provide project principles and development guidelines
                     ▼
┌─────────────────────────────────────────────┐
│ 2. Create Specification                     │ ◀─── update feature specs ───┐
│    Draft Change Proposal                    │                              │
└────────────────────┬────────────────────────┘                              │
                     │                                                       │
                     │ share intent from product perspective                 │
                     ▼                                                       │
┌─────────────────────────────────────────────┐                              │
│ 3. Plan Architecture                        │──────────────────────────────┘
│    Analyse and provide architecture choices │◀─── update architecture ─────┐
└────────────────────┬────────────────────────┘                              │
                     │                                                       │
                     │ design target architecture                            │
                     ▼                                                       │
┌─────────────────────────────────────────────┐                              │
│ 4. Break Down into Tasks                    │──────────────────────────────┘
│    Create actionable task list              │
└────────────────────┬────────────────────────┘
                     │
                     │ break down into tasks
                     ▼
┌─────────────────────────────────────────────┐
│ 5. Review & Align                           │ ◀─── LLM updates tasks ─────┐
│    (edit specs/tasks)                       │                             │
└────────────────────┬────────────────────────┘                             │
                     │                                                      │
                     │ approved plan                                        │
                     ▼                                                      │
┌─────────────────────────────────────────────┐                             │
│ 6. Implement Tasks                          │─────────────────────────────┘
│    (AI writes code)                         │
└────────────────────┬────────────────────────┘
                     │
                     │ ship the change
                     ▼
┌─────────────────────────────────────────────┐
│ 7. Review Code Changes                      │
│    Find issues and missing tests            │
└────────────────────┬────────────────────────┘
                     │
                     │ fix issues and add tests
                     ▼
┌─────────────────────────────────────────────┐
│ 8. Document Changes                         │
│    Document to docs/ directory              │
└─────────────────────────────────────────────┘
```

### 1. Setup project standards

Use the `/sdd:00-setup` command to create your project's governing principles and development guidelines that will guide all subsequent development.

```bash
/sdd:00-setup Use NestJS, follow SOLID and Clean Architecture
```

It can be used just once, in subsequent features development you can start from the next step.

### 2. Create specification

Use the `/sdd:01-specify` command to describe what you want to build. Focus on the what and why, not the tech stack.

```bash
/sdd:01-specify Add OAuth authentication with Google and GitHub providers
```

After LLM completes, you can update draft of change proposal that will be used as basis for future phases of development.

### 3. Plan architecture

Use the `/sdd:02-plan` command to provide your tech stack and architecture choices.

```bash
/sdd:02-plan Use libraries instead of direct integration
```

After LLM completes, you can review the proposal architecture, till you agree with the proposed design choices.

### 4. Break down into tasks

Use the `/sdd:03-tasks` command to create an actionable task list from your implementation plan.

```bash
/sdd:03-tasks Use TDD approach and prioritize MVP features
```

After LLM completes, you can update the task list to reflect your priorities

### 5. Implement features

Use the `/sdd:04-implement` command to execute all tasks and build your feature according to the plan.

```bash
/sdd:04-implement Focus on test coverage and error handling
```

During implementation LLM will update the task list with progression of the implementation.

### 6. Review code

Use the `/code-review:review-local-changes` command to review your changes.

```bash
/code-review:review-local-changes
```

LLM will review code quality and missing tests. If something incorrect, you can ask LLM to fix it.

### 7. Document changes

Use the `/sdd:05-document` command to document your changes.

```bash
/sdd:05-document Include API examples and integration guide
```

LLM will document completed changes to `docs/` directory, to provide basis for future development.

### 8. Create pull request

Use the `/git:create-pr` command to create a pull request for your changes.

```bash
/git:create-pr #123
```
