# Context Engineering Kit - Project Overview

## Purpose
Claude Code plugin marketplace with advanced context engineering techniques focused on improving agent result quality.

## Tech Stack
- **Content**: Markdown files (prompts, documentation)
- **Configuration**: YAML and JSON (plugin manifests, marketplace)
- **Build Tool**: Make
- **Documentation**: GitBook

## Project Structure
```
context-engineering-kit/
├── .claude-plugin/marketplace.json  # Main marketplace manifest
├── plugins/                          # Plugin source code
│   └── <plugin-name>/
│       ├── .claude-plugin/plugin.json
│       ├── README.md
│       ├── commands/                 # Slash commands (*.md)
│       ├── skills/                   # Skills (*.md)
│       └── agents/                   # Agent definitions (*.md)
├── docs/                             # GitBook documentation
├── specs/                            # Feature specifications
└── .beads/                           # Task tracking (Minibeads)
```

## Available Plugins
code-review, customaize-agent, ddd, docs, git, kaizen, mcp, reflexion, sadd, sdd, tdd, tech-stack
