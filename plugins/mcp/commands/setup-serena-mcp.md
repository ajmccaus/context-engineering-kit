---
name: setup-serena-mcp
description: Guide for setup Serena MCP server for semantic code retrieval and editing capabilities
argument-hint: Optional - specific configuration preferences or client type
---

User Input:

```text
$ARGUMENTS
```

# Guide for setup Serena MCP server

## 1. Check if Serena MCP server is already setup

Check whether you have access to Serena MCP server by attempting to use one of its tools (e.g., `find_symbol` or `list_symbols`).

If no access, proceed with setup.

## 2. Load Serena documentation

Read the following documentation to understand Serena's capabilities and setup process:

- Load <https://raw.githubusercontent.com/oraios/serena/refs/heads/main/README.md> to understand what Serena is and its capabilities
- Load <https://oraios.github.io/serena/02-usage/020_running.html> to learn how to run Serena
- Load <https://oraios.github.io/serena/02-usage/030_clients.html> to learn how to configure your MCP client

## 3. Guide user through setup process

Based on the loaded documentation:

1. **Check prerequisites**: Verify that `uv` is installed (required for running Serena)
2. **Identify client type**: Determine which MCP client the user is using (Claude Code, Claude Desktop, Cursor, VSCode, etc.)
3. **Provide setup instructions**: Guide through the configuration specific to their client
4. **Test connection**: Verify that Serena tools are accessible after setup

## 4. Update CLAUDE.md file

Once Serena is successfully set up, update CLAUDE.md file with the following content:

```markdown
### Use Serena MCP for Semantic Code Analysis instead of regular code search and editing

Serena MCP is available for advanced code retrieval and editing capabilities.

- Use Serena's tools for precise code manipulation in structured codebases
- Prefer symbol-based operations over file-based grep/sed operations

```

## 5. Project initialization (if needed)

If this is a new project or Serena hasn't been initialized:

1. Guide user to run project initialization commands
2. Explain project-based workflow and indexing
3. Configure project-specific settings if needed
