# Agentic Coding Field Manual

I was skeptical about AI coding until I hurt my hand and had to find a better way to keep shipping. I didn't want to fall into "vibe coding" - just accepting whatever the model spits out and hoping it works. So I distilled a workflow from what I've learned and what others have proven - one that keeps you in control without sacrificing speed.

That's what this manual is about.

## Who This Is For

Engineers who ship production software and want to move faster without losing their judgment. Not hobbyists. Not people who want AI to "build my app."

If you don't already have opinions about linting, type systems, and test strategy, start there first. This manual assumes you do.

## The Core Problem

The biggest risk of AI coding isn't bad code - linters catch that. It's **loss of agency**. A gradual, barely noticeable surrender of control over your own codebase. It shows up in five ways:

**You stop thinking.** The model always has an answer, so you stop forming your own. Architectural instincts atrophy. Six months in, you can't design a system without prompting an LLM first.

**You stop reading.** Diffs get accepted with a glance. You build on unreviewed code, then ask the AI to fix what breaks. You become a passenger in your own codebase.

**You get average output.** The model produces the statistical mean of its training data. Default configs, tutorial patterns, mock-heavy tests that pass but verify nothing. Without constraints, everything converges to mediocre.

**You lose context.** Long sessions degrade. The model contradicts earlier decisions, introduces subtle inconsistencies, and drifts from the plan. Each individual diff looks fine; the aggregate doesn't.

**You mistake speed for quality.** Shipping faster feels like shipping better. Design thinking gets skipped because "I can always regenerate it." But regeneration without judgment is just churn.

Every chapter in this manual targets at least one of these. The goal is the same throughout: you stay in control.

## The Mental Model

AI is a fast typist with encyclopedic knowledge and zero judgment. It produces statistically average code - the mean of everything it's seen. Left unconstrained, you get the average of every tutorial, every Stack Overflow answer, every GitHub repo in its training data.

Your job is to constrain it: tell it *what* to build, *how* to build it, and reject anything that doesn't meet your standards.

You drive. AI types.
