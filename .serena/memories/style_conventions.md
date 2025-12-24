# Style and Conventions

## Plugin Design Philosophy

1. **Commands over skills** - Commands load on-demand; skill descriptions load into context by default
2. **Specialized agents** - Use agents with focused context to reduce hallucinations
3. **Setup-commands** - Use setup commands to update CLAUDE.md for persistent project context
4. **Minimal tokens** - Every token counts; keep prompts concise

## Writing Guidelines

- **Prompts should be concise** - Every token counts
- **Be specific** - Avoid vague or overly general instructions
- **Focus on quality** - Better to do one thing well than many things poorly
- **Use MUST and SHOULD tags** - Clearly describe requirements for agents
- **Use examples** - Show how to behave in different scenarios

## File Organization

- Plugin manifests: `plugins/<name>/.claude-plugin/plugin.json`
- Commands: `plugins/<name>/commands/<command>.md`
- Skills: `plugins/<name>/skills/<skill>/SKILL.md`
- Agents: `plugins/<name>/agents/<agent>.md`
- Documentation: `docs/plugins/<name>/README.md`

## Version Management

- NEVER modify versions manually
- Use `make set-version PLUGIN=<name> VERSION=<x.y.z>` for plugin versions
- Use `make set-marketplace-version VERSION=<x.y.z>` for marketplace version

## README Sync

Keep README.md in sync between `plugins/<name>/` and `docs/plugins/<name>/`:
- `make sync-docs-to-plugins` - Copy from docs to plugins
- `make sync-plugins-to-docs` - Copy from plugins to docs
