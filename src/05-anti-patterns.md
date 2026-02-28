# 05 - Anti-Patterns

## When NOT to Use AI

AI accelerates implementation of known solutions. It's net-negative for:

* **Architecture decisions** - It gives you a plausible-sounding answer that's the average of everything it's seen. Your system isn't average. Use plan mode to rubber-duck, then decide yourself.
* **Security-critical code** - Auth flows, encryption, access control. One subtle bug is a breach, and the model will confidently produce subtle bugs.
* **Performance-sensitive paths** - Hot loops, memory layout, cache behavior. AI writes *readable* code, not *fast* code.
* **Novel algorithms** - If the solution doesn't exist in training data, the model will hallucinate something that looks right and passes type checks. Plausible nonsense.

For these, think yourself. Write the code yourself. Use AI to review it, not produce it.

## The Acceptance Spiral

The most dangerous pattern in agentic coding:

1. AI writes code. You glance at it. Looks fine. Accept.
2. AI writes more on top. You glance. Accept.
3. You now have 500 lines of unreviewed code as your foundation.
4. Something breaks. You don't understand the code because you never read it.
5. You ask AI to fix it. It adds more unreviewed code.
6. You are now a passenger.

This is how engineers stop thinking. It doesn't happen in one moment - it's a gradual surrender of agency, one lazy diff review at a time.

Break the cycle: **read every diff before committing.** Every one. If you can't explain what changed and why, you don't commit it.

## Common Failure Modes

**Plausible nonsense** - Code that looks correct, passes type checks, and has subtle logic errors. AI is excellent at producing code that *seems* right. The type system catches structural issues; it doesn't catch "this algorithm doesn't actually do what we need."

**Over-abstraction** - AI loves creating abstractions: factories, wrappers, base classes, utility functions, generic helpers. Most of the time, the concrete solution is better. When you see the model creating `AbstractHandlerFactory`, intervene.

**Ignoring existing patterns** - Your codebase handles errors one way. AI adds a new function doing it differently because that's what it's seen more often in training data. AGENTS.md mitigates this but doesn't eliminate it. You still need to review.

**Cargo-culting popular patterns** - The model gravitates toward whatever has the most representation in its training data, regardless of whether it fits your context. You get Redux in a project that doesn't need state management, or a full ORM when raw queries would do.

**Scope creep** - You ask for one change. The model "helpfully" refactors three nearby files. Each refactor is individually reasonable but collectively they've changed the semantics of code you didn't ask it to touch.

## How to Review AI Output

Read diffs, not files. What changed matters more than what exists.

Checklist:

* Does it follow AGENTS.md constraints?
* Did it modify files you didn't ask it to touch?
* Are there new abstractions that aren't justified?
* Does the approach match what you agreed in the plan phase?
* Would you approve this in a code review from a human?

That last question is the filter. If a junior dev submitted this PR, would you merge it? Apply the same standard.

## The Three-Attempt Rule

If the AI can't produce acceptable output in three tries for the same task, stop. Write it yourself.

Some tasks are faster by hand. Spending 30 minutes prompt-engineering a solution you could write in 10 isn't "being productive with AI" - it's sunk cost fallacy with extra steps.

Know when to take the wheel.
