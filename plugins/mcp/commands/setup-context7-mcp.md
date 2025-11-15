---
name: setup-context7-mcp
description: Guide for setup Context7 MCP server to load documentation for specific technologies.
argument-hint: List of languages and frameworks to load documentation for
---

User Input:

```text
$ARGUMENTS
```

# Guide for setup Context7 MCP server

## 1. Check if Context7 MCP server is alredy setup

Check whether you have access to Context7 MCP server by making request.

if no, load <https://raw.githubusercontent.com/upstash/context7/refs/heads/master/README.md> file and guide user through setup process that applicable to agent/operation system.

## 2. Update CLAUDE.md file

- Parse user input, if it empty read current proejct structure and used technologies, if project empty ask user to provide list of languages and frameworks that planned to be used in this project.
- Search through context7 MCP for relevant technologies documentation
- Update CLAUDE.md file with following content:

```markdown
### Use Context7 MCP for Loading Documentation

Context7 MCP is available to fetch up-to-date documentation with code examples.

**Recommended library IDs**:

- `[doc-id]` - short description of documentation

```
