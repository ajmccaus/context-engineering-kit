# Concepts

Reference of terms and concepts used throughout Context Engineering Kit documentation.

## Commands

Commands are explicit actions you invoke manually to perform specific tasks. They follow the pattern `/plugin-name:command-name`. They include a prompt that will be loaded to the LLM and trigger it to perform a specific task.

Commands are:

- **User-invoked** - You explicitly call them when needed
- **Task-specific** - Designed for particular operations
- **Token-efficient** - Don't consume context when not in use

### Discovering Commands

After installing a plugin, its commands become available. View all commands:

```bash
/help
```

Commands are namespaced by plugin, making their origin clear:

- `/reflexion:reflect` - From the reflexion plugin
- `/git:commit` - From the git plugin
- `/sdd:01-specify` - From the sdd plugin

### Command Syntax

```bash
/plugin-name:command-name [optional-argument]
```

**Examples:**

```bash
# No argument required
/reflexion:reflect
/git:commit

# With argument
/sdd:01-specify Add user authentication with OAuth
/kaizen:analyse Target the checkout flow for optimization
```

---

## Using Skills

Skills are automatically applied knowledge that influences Claude's behavior without explicit invocation. When a skill is loaded, Claude considers it continuously during your session.

### How Skills Work

Skills are markdown documents loaded into Claude's context that provide:

- **Best practices** - Industry-standard approaches and patterns
- **Methodologies** - Systematic processes (e.g., TDD, DDD)
- **Anti-patterns** - Common mistakes to avoid
- **Gate functions** - Checks before taking certain actions

**Key difference from commands:**

- **Commands** - You invoke manually with `/command-name`
- **Skills** - Claude applies automatically when relevant, but you can explicitly ask Claude to load a specific skill, for example: "load TDD skill, before implementing feature"

### Skills vs Commands Trade-off

**Why prefer commands over skills:**

The Context Engineering Kit architecture prefers commands over skills to minimize token usage:

- **Skills description** always populate context (every session)
- **Commands** only load when invoked (zero tokens until used)

---

## Agents

Agents are specialized sub-agents designed for focused tasks. Unlike Claude's general-purpose capabilities, agents have specific expertise and domain knowledge.

### What Are Agents?

Agents are fresh Claude instances launched with specialized prompts for specific tasks. They:

- **Focus on one domain** - e.g., code review, architecture design, business analysis
- **Have specialized knowledge** - Expertise in their specific area
- **Work independently** - Operate as sub-agents with their own context
- **Return specific outputs** - Structured results aligned with their purpose

### Agent Architecture

```
Main Claude Session (You)
├── Launches specialized agent for task
│   ├── Agent has specific prompt and knowledge
│   ├── Agent performs focused work
│   └── Agent returns structured result
└── Integrates agent results into workflow
```

**Key benefits:**

- **Fresh context** - Each agent starts with clean context specific to its task
- **Specialized expertise** - Domain-specific knowledge and techniques
- **Parallel execution** - Multiple agents can work simultaneously
- **Quality gates** - Agents perform validation and review

### How Agents Are Invoked

**Automatic invocation** - Commands launch agents as part of their workflow:

```bash
# Launches multiple code review agents
/code-review:review-local-changes

# Launches business-analyst agent
/sdd:01-specify Add user authentication

# Launches researcher, code-explorer, and software-architect agents
/sdd:02-plan
```

**Manual invocation** - Request specific agents directly:

```text
Launch business analyst agent to analyze payment feature requirements
Launch code explorer agent to trace authentication flow
Launch software architect Opus agent to design caching strategy
```

## Working with CLAUDE.md

The `CLAUDE.md` file is your project's living memory - a central repository of project-specific knowledge, patterns, and insights that persists across sessions.

### What is CLAUDE.md?

`CLAUDE.md` is a markdown file in your project root that contains:

- **Project constitution** - Core principles and standards
- **Architecture decisions** - Key design choices and rationale
- **Best practices** - Project-specific patterns and conventions
- **Lessons learned** - Insights from reflections and critiques
- **Common pitfalls** - Known issues and how to avoid them
- **Tech stack guidance** - Framework and library usage patterns

**Why it matters:**

- **Persistent memory** - Knowledge survives between sessions
- **Consistency** - Ensures Claude follows project patterns
- **Quality improvement** - Accumulates insights over time
- **Team alignment** - Documents shared understanding

### How Plugins Update CLAUDE.md

Several plugins read from and write to `CLAUDE.md`:

**Reflexion plugin** - Memorizes insights:

```bash
# After reflecting, save insights to CLAUDE.md
/reflexion:memorize
```

**What it adds:**

- Key insights from reflection sessions
- Patterns discovered during implementation
- Lessons learned from critiques
- Common mistakes to avoid
- Successful approaches to replicate

**Tech Stack plugin** - Adds language/framework practices:

```bash
/tech-stack:add-typescript-best-practices
```

**What it adds:**

- Language-specific best practices
- Framework usage patterns
- Code style guidelines
- Common anti-patterns for the tech stack

**DDD plugin** - Sets up code quality standards:

```bash
/ddd:setup-code-formating
```

**What it adds:**

- Code formatting rules
- Architecture principles
- SOLID principle applications
- Clean Architecture patterns

**MCP plugin** - Documents MCP server requirements:

```bash
/mcp:setup-context7-mcp
```

**What it adds:**

- MCP server integration requirements
- When and how to use specific MCP servers
- Configuration and usage patterns

**SDD plugin** - Establishes project constitution:

```bash
/sdd:00-setup Use NestJS, follow SOLID and Clean Architecture
```

**What it adds:**

- Project constitution and governance
- Core architectural principles
- Technology stack decisions
- Development standards

