---
title: Add hooks to reflect plugin
status: open
priority: 2
issue_type: feature
created_at: 2025-12-24T05:39:05.383249682+00:00
updated_at: 2025-12-24T05:44:29.606955559+00:00
---

# Description

Implement hooks integration for the reflexion plugin with the following specification:

**Trigger Condition:**
- Trigger /reflect command automatically after an agent STOP event
- Only trigger if the word 'reflect' was present in the agent's initial prompt

**Implementation Requirements:**
- Add agent-stop hook to reflexion plugin
- Parse agent prompt to check for 'reflect' keyword
- Execute reflexion:reflect command when conditions are met
- Ensure hook works with all agent types (Task, specialized agents, etc.)

**Example Flow:**
1. User launches agent with prompt containing 'reflect': 'Implement feature X and reflect on the approach'
2. Agent completes work and stops
3. Hook automatically triggers /reflect command
4. Reflection results are presented to user

---

## Files to be Modified/Created

### Primary Changes:
- `plugins/reflexion/.claude-plugin/plugin.json` - Add hooks configuration metadata
- `plugins/reflexion/hooks/agent-stop.js` or `.sh` - NEW: Hook script that checks for 'reflect' keyword and triggers command
- `plugins/reflexion/.claude/settings.json` - NEW: Hook registration (or document how to add manually)

### Documentation Updates:
- `plugins/reflexion/README.md` - Document the new automatic hook feature
- `docs/plugins/reflexion/README.md` - Sync documentation with make command

---

## Useful Resources for Implementation

### Hook Implementation Patterns:
- `plugins/customaize-agent/commands/create-hook.md` - Complete guide on hook creation, input/output formats, testing
- `plugins/mcp/commands/setup-codemap-cli.md` - Real example of hook implementation with settings.json configuration (lines 145-243)
- Official docs: https://docs.claude.com/en/docs/claude-code/hooks.md

### Reflexion Plugin Context:
- `plugins/reflexion/commands/reflect.md` - The command to be triggered (understand its behavior)
- `plugins/reflexion/.claude-plugin/plugin.json` - Plugin manifest structure
- `plugins/reflexion/README.md` - Current plugin documentation

### Development Guidelines:
- `docs/guides/custom-extensions.md` - Workflow for creating hooks (lines 146-241)
- `CONTRIBUTING.md` - Plugin development rules and structure
- `CLAUDE.md` - Project-specific development commands

### Agent Execution Patterns:
- `plugins/sdd/agents/developer.md` - Agent completion patterns
- `plugins/sadd/skills/subagent-driven-development/SKILL.md` - Agent execution and lifecycle
