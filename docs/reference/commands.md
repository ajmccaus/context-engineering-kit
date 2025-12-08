# Commands Reference

Complete alphabetical index of all commands available across all Context Engineering Kit plugins.

## Commands by Plugin

### Reflexion

Reflection and self-improvement commands based on Self-Refine and Reflexion papers. [More info](../plugins/reflexion/README.md).

- `/reflexion:reflect` - Reflect on previous response and output, based on Self-refinement framework for iterative improvement with complexity triage and verification 
- `/reflexion:memorize` - Memorize insights from reflections and updates CLAUDE.md file with this knowledge. Curates insights from reflections and critiques into CLAUDE.md using Agentic Context Engineering 
- `/reflexion:critique` - Comprehensive multi-perspective review using specialized judges with debate and consensus building 


### Code Review

Comprehensive code review commands using specialized agents. [More info](../plugins/code-review/README.md).

- `/code-review:review-local-changes` - Comprehensive review of local uncommitted changes using specialized agents with code improvement suggestions 
- `/code-review:review-pr` - Comprehensive pull request review using specialized agents 

### Git

Commands for Git operations including commits and pull requests. [More info](../plugins/git/README.md).

- `/git:commit` - Create well-formatted commits with conventional commit messages and emoji 
- `/git:create-pr` - Create pull requests using GitHub CLI with proper templates and formatting 
- `/git:analyze-issue` - Analyze a GitHub issue and create a detailed technical specification 
- `/git:load-issues` - Load all open issues from GitHub and save them as markdown files 

### Spec-Driven Development (SDD)

Complete Spec-Driven Development workflow commands. [More info](../plugins/sdd/README.md).

- `/sdd:00-setup` | Create or update the project constitution from interactive or provided principle inputs 
- `/sdd:01-specify` - Create or update the feature specification from a natural language feature description 
- `/sdd:02-plan` - Plan the feature development based on the feature specification 
- `/sdd:03-tasks` - Create detailed implementation tasks from feature plans with complexity analysis 
- `/sdd:04-implement` - Execute feature implementation following task list with TDD approach and quality review 
- `/sdd:05-document` - Document completed feature implementation with API guides, architecture updates, and lessons learned 
- `/sdd:brainstorm` - Refines rough ideas into fully-formed designs through collaborative questioning and exploration 

### Kaizen

Continuous improvement and problem analysis commands. [More info](../plugins/kaizen/README.md).

- `/kaizen:analyse` - Auto-selects best Kaizen method (Gemba Walk, Value Stream, or Muda) for target analysis 
- `/kaizen:analyse-problem` - Comprehensive A3 one-page problem analysis with root cause and action plan 
- `/kaizen:why` - Iterative Five Whys root cause analysis drilling from symptoms to fundamentals 
- `/kaizen:root-cause-tracing` - Systematically traces bugs backward through call stack to identify source of invalid data or incorrect behavior 
- `/kaizen:cause-and-effect` - Systematic Fishbone analysis exploring problem causes across six categories 
- `/kaizen:plan-do-check-act` - Iterative PDCA cycle for systematic experimentation and continuous improvement 

### Customaize Agent

Commands for creating and testing custom Claude Code extensions. [More info](../plugins/customaize-agent/README.md).

- `/customaize-agent:create-command` - Interactive assistant for creating new Claude commands with proper structure and patterns
- `/customaize-agent:create-skill` - Guide for creating effective skills with test-driven approach
- `/customaize-agent:create-hook` - Create and configure git hooks with intelligent project analysis and automated testing
- `/customaize-agent:test-skill` - Verify skills work under pressure and resist rationalization using RED-GREEN-REFACTOR cycle
- `/customaize-agent:test-prompt` - Test any prompt (commands, hooks, skills, subagent instructions) using RED-GREEN-REFACTOR cycle with subagents
- `/customaize-agent:apply-anthropic-skill-best-practices` - Comprehensive guide for skill development based on Anthropic's official best practices

### Test-Driven Development (TDD)

Test-first development methodology with agent-orchestrated coverage. [More info](../plugins/tdd/README.md).

- `/tdd:write-tests` - Systematically add test coverage for local code changes using specialized review and development agents
- `/tdd:fix-tests` - Fix failing tests after business logic changes or refactoring using orchestrated agents

### Docs

Documentation management commands. [More info](../plugins/docs/README.md).

- `/docs:update-docs` - Update implementation documentation after completing development phases

### Domain-Driven Development (DDD)

Commands for setting up domain-driven development practices. [More info](../plugins/ddd/README.md).

- `/ddd:setup-code-formating` - Sets up code formatting rules and style guidelines in CLAUDE.md

### Tech Stack

Commands for language and framework-specific best practices. [More info](../plugins/tech-stack/README.md).

- `/tech-stack:add-typescript-best-practices` - Setup TypeScript best practices and code style rules in CLAUDE.md

### MCP

Commands for integrating Model Context Protocol servers. [More info](../plugins/mcp/README.md).

- `/mcp:setup-context7-mcp` - Guide for setup Context7 MCP server to load documentation for specific technologies
- `/mcp:setup-serena-mcp` - Guide for setup Serena MCP server for semantic code retrieval and editing capabilities
- `/mcp:build-mcp` - Guide for creating high-quality MCP servers that enable LLMs to interact with external services
