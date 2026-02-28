# Agentic Coding Field Manual

Practical guidance for engineers who want to use AI coding agents without dropping production standards.

## Read Online

- GitHub Pages: <https://drsh4dow.github.io/agentic-coding-field-manual/>

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

- `src/` - Markdown source for chapters and appendices
- `book.toml` - mdBook configuration
- `.github/workflows/` - CI and deployment workflows

## Publishing Notes

Release hygiene is tracked in `RELEASE_CHECKLIST.md`.

## License

MIT. See `LICENSE`.
