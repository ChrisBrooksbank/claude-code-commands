# Claude Code Commands

Custom slash commands for [Claude Code](https://docs.anthropic.com/en/docs/claude-code).

## Commands

| Command | Description |
|---------|-------------|
| `/cb-new-project` | Scaffold a TypeScript project with LLM guardrails (ESLint, Prettier, Husky, Knip, Vitest, CI) |
| `/cb-guard-rails` | Add guard rails to existing projects - detect tech stack and add missing linting, formatting, pre-commit hooks, testing, visual regression & interaction testing (Playwright), and CI |
| `/cb-ralph-wiggum` | Scaffold Ralph Wiggum autonomous AI development loop with JTBD specs and bash iteration |

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

- `/cb-new-project` - Scaffold a new TypeScript project with quality guardrails
- `/cb-guard-rails` - Retrofit existing projects with quality guardrails (supports TS/JS, Python, Go, Rust). For web projects, adds Playwright visual regression screenshots and interaction smoke tests.
- `/cb-ralph-wiggum` - Set up autonomous AI development with fresh-context bash loop

## License

MIT
