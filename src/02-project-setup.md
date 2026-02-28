# 02 - Project Setup

Three things to set up before AI touches your codebase: scaffolding, guardrails, and AGENTS.md.

## Scaffold It Yourself

Let the AI scaffold your project and you get the lowest common denominator of every tutorial it's ever seen - Create React App patterns in 2026, default ESLint configs, boilerplate folder structures nobody actually wants.

Do it yourself:

* Create the base with your preferred tooling
* Set up a single validation command that covers linting, formatting, and type-checking - plus a separate test command
* Configure your linter with *your* preferences
* Configure your type checker with *your* preferences

The AI joins *after* the foundation is set. It works within your system, not its statistical average.

## Guardrail Tooling

The best way to prevent AI from writing bad code: make it impossible at the tooling level.

The AI can't merge garbage if the linter rejects it.

The examples below use TypeScript and Biome, but the pattern applies to any language and toolchain. Swap in your linter, your type checker, your runner.

### Linter at Max Strictness

Your linter is the first line of defense. Crank it to maximum strictness - whatever tool you use. Here's an example with [Biome](https://biomejs.dev/):

```json
{
  "assist": {
    "enabled": true,
    "actions": {
      "recommended": true,
      "source": {
        "organizeImports": "on",
        "useSortedKeys": "on",
        "useSortedProperties": "on"
      }
    }
  },
  "linter": {
    "enabled": true,
    "rules": {
      "recommended": true,
      "complexity": {
        "noExcessiveCognitiveComplexity": "warn",
        "noExcessiveLinesPerFunction": {
          "level": "warn",
          "options": { "maxLines": 100, "skipBlankLines": true }
        },
        "useMaxParams": "error"
      },
      "style": {
        "noEnum": "error",
        "noDefaultExport": "error",
        "noNestedTernary": "error",
        "useConsistentTypeDefinitions": {
          "level": "error",
          "options": { "style": "type" }
        },
        "useForOf": "error"
      },
      "suspicious": {
        "noConsole": {
          "level": "error",
          "options": {
            "allow": ["assert", "error", "info", "warn"]
          }
        },
        "noEvolvingTypes": "warn",
        "useErrorMessage": "warn"
      }
    }
  },
  "overrides": [
    {
      "includes": ["**/*.config.{js,cjs,mjs,ts,cts,mts}"],
      "linter": {
        "rules": {
          "style": { "noDefaultExport": "off" }
        }
      }
    }
  ]
}
```

This is a subset - a real config covers 50+ rules across complexity, style, correctness, and suspicious categories.

Note the `overrides` block: config files need default exports, so you loosen that specific rule for those files only. Start strict, loosen deliberately.

### Type System at Full Strictness

If your language has a type system, use its strictest settings. The AI loves to take shortcuts around type safety - strict mode forces it to deal with reality. Here's an example with TypeScript - not just `"strict": true`, go further:

```json
{
  "compilerOptions": {
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "exactOptionalPropertyTypes": true
  }
}
```

### The One-Command Validation Pattern

Wrap all your validation into a single command. One command, zero ambiguity - if it passes, the code meets your standards regardless of who wrote it.

In a TypeScript project with Biome, that might look like:

```json
{
  "scripts": {
    "check": "biome check",
    "typecheck": "tsc --noEmit",
    "lazycheck": "bun run check && bun run typecheck",
    "test": "bun test"
  }
}
```

After every AI change, run your validation command. The tool and the name don't matter. What matters is that it's one command, it covers everything, and you run it every time.

Tooling catches structural problems. For everything else - style, workflow, architectural boundaries - you need to tell the model how you work.

## AGENTS.md

AGENTS.md is the system prompt for your repo. It's always in the model's context window - tools like OpenCode and Cursor read it automatically. Claude Code reads `CLAUDE.md` instead, because Anthropic wasn't going to use a name they didn't invent. Same concept, different filename - just symlink it and move on.

You don't need one on day one. Start without it. When the model makes the same mistake twice - wrong branch, bad error handling pattern, style you hate - that's when you add a rule. AGENTS.md grows from friction, not from planning.

### Write It By Hand, Keep It Short

Tools like OpenCode offer `/init` to generate one. Don't. Auto-generated files are too verbose, not constraint-first, and miss your actual preferences. You know your codebase better than any model.

Keep it under 100 lines. The shorter it is, the more the model follows it. A 300-line AGENTS.md gets skimmed and mostly ignored - same as humans with long READMEs.

### Constraint-First

Tell the model what it *can't* do, not why. Short imperatives, not explanations. The example at the end of this section shows the style - every line is a constraint or a rule, nothing is a rationale.

### Patterns That Earn Their Place

Only add rules that address patterns the AI actually gets wrong. Common ones:

* **Style violations** - The model defaults to its training distribution. State your preferences explicitly.
* **Tool-specific commands** - Exact commands. The model will guess wrong otherwise.
* **Architectural boundaries** - What not to touch, where tests live, which branch to use.
* **Error handling style** - Without guidance, every function gets wrapped in try/catch.
* **When to ask for help** - Without this, the model guesses and plows forward.

### Example: Root AGENTS.md

```markdown
- Prefer automation: execute requested actions without confirmation unless
  blocked by missing info or safety/irreversibility.
- Assume senior-level knowledge. No explanations for standard practices.
  Terse, scannable, constraint-first.
- Branch from `staging`.
- Always rebase, never merge.
- After changes, run: `bun run lazycheck`.
- Always check if local server is running to use that instead of spawning
  new processes.
- Don't ignore linting or type errors - fix them, or ask for help if unsure.
- If build mode requires human input (a decision, question, or manual step),
  pause and request it explicitly, then continue once provided.
- Don't turn off rules without a very good reason. Bring it up for discussion
  instead of bypassing it.

## Style Guide

- Avoid `try`/`catch` where possible
- Prefer single word variable names where possible
- Rely on type inference; avoid explicit annotations unless necessary
  for exports or clarity
- Type casting is a last resort - prefer type guards and runtime checks
- When uncertain about a library, use `context7` tools
- If using webfetch, look for llms.txt in the page's root first

## Testing

- Avoid mocks as much as possible
- Test actual implementation, do not duplicate logic into tests
- Prefer package-level tests over workspace-level tests

## Plan Mode

- Make the plan extremely concise. Sacrifice grammar for brevity.
- End each plan with unresolved questions, if any.
```

About 30 lines. Covers style, workflow, testing philosophy, and plan mode behavior. The Plan Mode section is borrowed from [Matt Pocock](https://www.aihero.dev/).

## Monorepo: Root + Leaf

> Single repo? Skip this section.

In a monorepo, AGENTS.md files cascade:

* **Root `AGENTS.md`** - Transversal rules. Git workflow, code style, testing philosophy. Applies everywhere.
* **Leaf `AGENTS.md`** (per package) - Inherits root, adds package-specific constraints.

A leaf looks like:

```markdown
- Inherit global rules from root `AGENTS.md`.
- Use semantic tokens for colors to support dark mode and themes;
  avoid hardcoded values.
- No arbitrary Tailwind values - use design tokens unless explicitly required.
- UI changes must be verified with screenshots using playwright MCP tools.

## Patterns

### Component Decision Tree (strict order)

1. Shadcn component exists? -> use it
2. Reusable (3+ uses)? -> `src/components/ui/`
3. Section-specific? -> `src/components/[section]/`
4. One-off layout? -> semantic HTML
```

The first line - "Inherit global rules from root AGENTS.md" - is critical. Without it, the model may ignore root-level constraints when working inside a package.

Keep leaves even shorter than root. Package-specific rules only. If a rule applies everywhere, it belongs in root.
