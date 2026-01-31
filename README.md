# Claude Code Commands

Custom slash commands for [Claude Code](https://docs.anthropic.com/en/docs/claude-code).

## Commands

| Command | Description |
|---------|-------------|
| `/cb-new-project` | Scaffold a TypeScript project with LLM guardrails (ESLint, Prettier, Husky, Knip, Vitest, CI) |

## Installation

Copy the `.md` files to your Claude Code commands directory:

```bash
# macOS/Linux
cp *.md ~/.claude/commands/

# Windows
copy *.md %USERPROFILE%\.claude\commands\
```

Then restart Claude Code or start a new session.

## Usage

In Claude Code, type `/cb-new-project` to scaffold a new TypeScript project with quality guardrails.

## License

MIT
