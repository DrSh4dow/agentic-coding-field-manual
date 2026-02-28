# 03 - The Loop

Plan. Execute. Test. Commit. Repeat.

The goal: never lose control of what's happening. This is the workflow that separates agentic coding from vibe coding.

```text
  Plan ──> Execute ──> Test ──> Commit
   ^                              |
   └──────────────────────────────┘
```

## Plan

Use plan mode. Think through the approach *together* before any code is written.

In OpenCode, toggle between plan and build with `Tab`.

A good plan prompt:

```text
I need to add rate limiting to the API endpoints.
Requirements: per-user, 100 req/min, 429 response with retry-after header.
Plan the implementation.
```

The model proposes an approach. You review. You adjust. You agree. *Then* you execute.

Plans should be a numbered list of steps, not an essay. If the plan is longer than 10 lines, the scope is too big - break it down.

### What Makes a Good Plan

* Concise - a list, not a narrative
* Scoped - one feature, one bug, one refactor
* Specific about files and functions it will touch
* Ends with unresolved questions if any remain

## Execute

Switch to build mode. The AI writes code that matches the agreed plan.

Key discipline: the model implements what was agreed, nothing more. If it starts freelancing - adding features you didn't ask for, refactoring nearby code, "improving" things - intervene. Don't wait until it's finished. This is the most common drift point.

### Steering Mid-Execution

You don't have to wait until the model finishes to course-correct. Watch what it's doing - the files it opens, the tool calls it makes, the hunks appearing in the diff. If something looks wrong, send a message. The model will read it and adjust.

**Soft redirect.** "We're adding rate limiting to the API, not refactoring the middleware - back on track." No need to stop or undo. Just nudge.

**Assist.** If the model is struggling with a library or API - looping on the same error, guessing at function signatures - point it to the right docs. Drop a link to the documentation, an llms.txt file, or a specific code example. The model works better with real references than with its training data.

**Scope anchoring.** When the model drifts, restate the original scope and ask why: "The plan was to update the user service. Why are you modifying the payment module? Revert that and stick to user service." The model lost the thread - remind it, and get an explanation if the detour wasn't obvious.

**Hard stop.** If it's too far gone, stop and revert. In OpenCode: `/undo`. Or use git. Don't spend time untangling a mess - reset and re-prompt with tighter constraints.

**Fork.** Different from correcting mistakes. Fork when you want to explore an alternative without losing your current state - "what if we used Redis instead of in-memory caching" or "let's try a different schema design." Forking is for exploration, not damage control.

### The CI Gate

The model runs your validation command and tests as part of execution - not as a separate step you do by hand. When checks fail, let the model fix its own mistakes. Don't hand-edit AI output.

But don't just watch passively either. Each failure is information:

* **Was the prompt unclear?** Tighten the next one.
* **Is this a recurring pattern?** Add a rule to AGENTS.md.
* **Did the linter or type checker miss it?** Strengthen your CI gates.

If the model can't self-correct after two or three attempts, the plan was wrong. Go back to plan mode with a fresh session.

## Test

CI passing means the code is structurally sound. It doesn't mean the feature works.

This step is yours. Run the feature. Click through the UI. Hit the endpoint. Check the actual behavior against what you intended. This is where you catch what automation can't - the code that passes every check but doesn't actually do what you need.

If something's off, go back to Execute with a specific correction: "The rate limiter returns 200 instead of 429 when the limit is exceeded." Give the model concrete behavior to fix, not vague feedback.

## Commit

Ship it. Then start the cycle again.

Rules:

* **Small, atomic commits.** One logical change per commit. Don't let the AI create monster diffs touching 20 files for what should be a 3-file change.
* **Read the diff before committing.** Every time. No exceptions. This is where "engineer stops thinking" starts - skipping diff review.
* **Always rebase, never merge.** Clean history. The model doesn't care, but you will when debugging at 2am.

## Context Management

Model output quality degrades as the conversation grows. The context window fills up, older instructions get diluted, and the model starts contradicting its earlier decisions.

### When to Kill and Start Fresh

* The model starts ignoring your AGENTS.md rules
* Output is increasingly sloppy or repetitive
* It loops on the same error without making progress
* You're 15+ messages deep and quality is dropping

Fresh context = fresh start. Don't cling to a degraded session. Starting over with a clear prompt is faster than fighting a confused model.

### Why Not Compact?

Most tools offer a way to summarize or compact the conversation (OpenCode: `/compact`, Claude Code: `/compact`). In theory, this frees up context without losing the thread. In practice, compaction degrades quality significantly - the model loses nuance, forgets constraints, and makes worse decisions on the summarized context than it would with a clean start.

Always prefer a fresh session over a compacted one. A new conversation with a clear prompt outperforms a compressed version of a long, degraded one.

### Context Budgeting

Think of each conversation as a budget. You have a finite amount of useful context before quality drops. Spend it on:

1. The plan (cheap - a few lines)
2. Execution (the bulk of it)
3. Error correction (expensive - each round adds noise)

If error correction is eating your budget, the plan was wrong. Go back to step 1 with a fresh session.
