# Task Completion Checklist

When completing a task in this project:

## For Plugin Changes

1. **Test the plugin** - Use `customaize-agent:test-prompt` or `customaize-agent:test-skill`
2. **Update version** - Run `make set-version PLUGIN=<name> VERSION=<x.y.z>`
3. **Sync documentation** - Run appropriate sync command:
   - `make sync-plugins-to-docs` if you edited plugins/
   - `make sync-docs-to-plugins` if you edited docs/
4. **Verify manifest** - Check `.claude-plugin/marketplace.json` is updated

## For Documentation Changes

1. Ensure README.md is synced between plugins/ and docs/
2. Update SUMMARY.md if adding new pages

## Before Committing

1. Check `git status` for all changed files
2. Review changes with `git diff`
3. Use conventional commit messages with emoji
4. Test that plugins work with Claude Code
