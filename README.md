# Agentic Coding Field Manual

A practical, opinionated manual for using AI coding agents with production-grade engineering discipline.

The biggest risk of AI coding isn't bad code - it's loss of agency. A gradual surrender of control over your own codebase. This manual gives you the workflow to move faster without losing your judgment. The core thesis: **you drive, AI types.**

## Who This Is For

Engineers who ship production software and want to move faster without losing their judgment. Not hobbyists. Not people who want AI to "build my app." This manual assumes you already have opinions about linting, type systems, and test strategy.

## What's Inside

| Chapter | Description |
|---------|-------------|
| [Introduction](https://drsh4dow.github.io/agentic-coding-field-manual/00-intro.html) | The core problem -- five ways AI erodes engineering agency -- and the mental model to prevent it. |
| [Tools](https://drsh4dow.github.io/agentic-coding-field-manual/01-tools.html) | What your AI coding tool needs (plan mode, LSP, forking), OpenCode setup, and MCP servers. |
| [Project Setup](https://drsh4dow.github.io/agentic-coding-field-manual/02-project-setup.html) | Scaffold it yourself, guardrail tooling (linter + type system at max strictness), and writing AGENTS.md. |
| [The Loop](https://drsh4dow.github.io/agentic-coding-field-manual/03-the-loop.html) | The Plan -> Execute -> Test -> Commit workflow that separates agentic coding from vibe coding. |
| [Testing](https://drsh4dow.github.io/agentic-coding-field-manual/04-testing.html) | No-mock philosophy -- test real behavior with real dependencies. Package-level test structure. |
| [Anti-Patterns](https://drsh4dow.github.io/agentic-coding-field-manual/05-anti-patterns.html) | When NOT to use AI, the acceptance spiral, common failure modes, and the three-attempt rule. |
| [Appendix: UI With Real Designs](https://drsh4dow.github.io/agentic-coding-field-manual/appendix-ui.html) | Screenshot-driven and token-driven approaches to turning real designs into code. |

## Read Online

[drsh4dow.github.io/agentic-coding-field-manual](https://drsh4dow.github.io/agentic-coding-field-manual/)

## Local Development

### Prerequisites

- [Bun](https://bun.sh/)
- [mdBook](https://rust-lang.github.io/mdBook/)

### Install Dependencies

```bash
bun install --frozen-lockfile
```

### Quality Checks

```bash
bun run lint:md
```

### Build The Book

```bash
mdbook build
```

The generated site is written to `book/`.

## Repository Structure

- `src/` -- Markdown source for all chapters and appendices
- `book.toml` -- mdBook configuration
- `.github/workflows/` -- CI (markdown linting) and GitHub Pages deployment

## Author

[Daniel Moretti](https://github.com/drsh4dow)

## License

MIT. See [LICENSE](LICENSE).
