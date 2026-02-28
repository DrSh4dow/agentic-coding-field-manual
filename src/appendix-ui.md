# Appendix - UI With Real Designs

AI is actually good at generating UI now. That's not the problem. The problem is that most products already have a design language - a designer delivered screens in Figma, there's an existing component library, there are color tokens and spacing scales that need to be respected. The challenge isn't getting the AI to produce something that looks nice. It's getting it to produce something that looks like *yours*.

This appendix covers two levels of constraint for turning real designs into code with AI assistance. They're not mutually exclusive - use tokens as the foundation and screenshots for specific implementations.

## Screenshot-Driven

Use this when a designer has delivered a specific screen or layout and you need to implement it faithfully. Good for full pages, one-off sections, and anything where the visual reference is the source of truth.

1. Designer delivers in Figma
2. Export the relevant screen or component as a screenshot
3. Feed the screenshot to the agent with constraints:

```text
Here's the design for the settings page [attach screenshot].
Implement it using existing components from the design system.
Use semantic color tokens - no hardcoded values.
No arbitrary Tailwind values.
Check the component tree before creating new components.
```

**Strengths:** Fast. Low ceremony. Works well for unique layouts where the design is already final.

**Limitations:** The model interprets the screenshot, so spacing and sizing won't be pixel-perfect. Expect to iterate on details.

## Token-Driven

Use this when your product has a design system and you want the AI to stay on-brand by default. Good for component systems, reusable UI, and anything that needs theme support.

1. Designer maintains a token system in Figma (colors, spacing, typography)
2. Export tokens to your codebase as CSS variables or Tailwind theme config
3. AI builds components constrained to those tokens

The constraint is built into the tooling - the model *can't* use `bg-blue-500` if your Tailwind config only exposes `bg-primary` and `bg-surface`. The design system enforces consistency, not discipline.

Add to your frontend package's AGENTS.md:

```markdown
- Use semantic tokens for colors to support dark mode and themes;
  avoid hardcoded values.
- No arbitrary Tailwind values - use design tokens unless explicitly required.
```

**Strengths:** Consistent output. Theme support built in. Less iteration needed.

**Limitations:** Requires upfront investment in the token system. Designer and engineer need to agree on the token structure.

## Verifying UI Changes

Use the Playwright MCP server (configured in [Chapter 01](01-tools.md)) to close the feedback loop.

Add to your AGENTS.md:

```markdown
- UI changes must be verified with screenshots using playwright MCP tools.
```

The model takes a screenshot after implementation, compares against the design, and iterates. This catches:

* Wrong tokens or colors
* Layout mismatches
* Missing responsive states
* Broken component composition

The model reviews its own output visually before you do. Most obvious issues get fixed before the diff reaches you.

## Implementing a Design

Don't let the model one-shot a full screen from a screenshot. It will inline everything into a single monolithic component — hardcoded values, duplicated patterns, no reuse. Instead, follow this workflow:

1. **Start from the screenshot.** Look at the design. What are the building blocks?
2. **Inventory check.** Do you already have the components needed? Check your component library, your existing custom components, your tokens.
3. **Build what's missing.** If the design needs components you don't have, create them first — in isolation, before assembling the full screen.
4. **Then assemble.** Compose the screen from existing + newly created components. Semantic HTML is for layout and glue code only, not for things that should be components.

The key: build the blocks first, then compose. This prevents the model from generating a wall of JSX with everything inlined.

When the model encounters a UI need during implementation, it should follow this priority:

```text
1. Component library already has it? -> use it
2. Reusable (3+ uses)? -> src/components/ui/
3. Section-specific? -> src/components/[section]/
4. One-off layout? -> semantic HTML
```

Add this to your frontend AGENTS.md:

```markdown
- Before implementing a design, inventory existing components that match.
- Build missing reusable components before assembling the full screen.
- Use semantic tokens for all values. No hardcoded colors, spacing, or typography.
- Semantic HTML is for layout and glue code only - not for repeating UI patterns.
```

Without these constraints, the model creates new components for everything. You end up with 40 button variants when your component library already handles it.
