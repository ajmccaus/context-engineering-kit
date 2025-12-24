# Suggested Commands

## Development Commands (Makefile)
```bash
make help                                       # Show all available commands
make list-plugins                               # List plugins with versions
make sync-docs-to-plugins                       # Copy docs/plugins/*/README.md → plugins/*/README.md
make sync-plugins-to-docs                       # Copy plugins/*/README.md → docs/plugins/*/README.md
make set-version PLUGIN=name VERSION=x.y.z     # Update plugin version
make set-marketplace-version VERSION=x.y.z     # Update marketplace version
```

## Task Management (Minibeads)
```bash
mb ready                    # Show issues ready to work
mb list --status=open       # List all open issues
mb show <id>                # View issue details
mb create "title" -t task   # Create new task
mb update <id> --status=in_progress  # Start working
mb close <id>               # Complete task
```

## Git Operations
```bash
git status                  # Check current state
git diff                    # View changes
codemap --diff --ref master # View changes vs master with structure
```

## Testing Plugins
Use Claude Code skills:
- `customaize-agent:test-prompt` - Test any prompt
- `customaize-agent:test-skill` - Test skill behavior
