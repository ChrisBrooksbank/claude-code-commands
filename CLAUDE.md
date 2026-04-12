# CLAUDE.md

## Project Overview

A collection of custom Claude Code slash commands for scaffolding and enhancing TypeScript projects.

## Commands

- `cb-new-project.md` — Scaffold a TypeScript project with LLM guardrails (ESLint, Prettier, Husky, Knip, Vitest, CI)
- `cb-guard-rails.md` — Add guard rails to existing projects
- `cb-ralph-wiggum.md` — Scaffold Ralph Wiggum autonomous AI development loop

## Repository Structure

Each `.md` file in the root is a slash command. Users copy files to `~/.claude/commands/`.

## Development

To modify a command, edit the relevant `.md` file. Commands are Markdown prompts with embedded instructions for Claude.

Keep command prompts focused, with clear interviewing steps and explicit file generation instructions.