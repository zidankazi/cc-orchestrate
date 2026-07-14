---
name: orchestrate
description: Lead-agent delegation loop — do recon inline, write one spec-quality brief, hand the implement/test/lint loop to the cheaper `sidekick` subagent, then review the diff and correct by re-briefing rather than rewriting. Use for work with a delegable implementation; skip it for live-debugging or judgment-heavy tasks where the accumulated context IS the work.
---

# orchestrate — lead delegates, sidekick implements

The premise (from Cognition's "Making Fable Cheaper Than Opus"): an agent's cost
is dominated by the **lead's** turn count and context size, not by per-token
price. Leads that delegate *early* with a good brief — and review instead of
rewriting — take a fraction of the turns and drag a fraction of the context.
This skill is that discipline: you (the lead) stay the manager; the `sidekick`
subagent (a cheaper, faster model with the house rules baked in) does the
implement/test/lint loop.

> This kit is an *implementation* of that pattern, not a reproduction of its
> benchmark. The cost/score numbers in the post are Cognition's, measured on
> their eval and harness. What this kit gives you is the loop.

## Step 0 — decide if this task is delegable at all

Delegation has no leverage when there is nothing to hand off. **Do the work
inline** — do NOT delegate — when:

- Success depends on live behavior only you can observe (a running service, a
  device, real timing/network) and can't be reviewed away from a diff.
- It's a serial debugging hunt where the root cause is one long chain of
  judgments — the accumulated context is the work.
- It's a handful of turns with nothing between deciding and shipping.

**Good candidates:** anything verifiable from tests, types, and the repo's own
gates — implementing a spec'd function, adding test coverage, a mechanical
refactor, a data/config change with a clear done-condition. If the task is
delegable but mixed, split it: delegate the verifiable part, keep the rest.

## Step 1 — recon inline, cheaply

Discover the work-list yourself before delegating — this is fast and it's what
makes the brief good. Read the owning code and its tests; grep for existing
helpers so the brief can say "reuse X" instead of the sidekick inventing one.

Do NOT start implementing. The moment you write the design decisions and pull
the important files into your context is the moment the expensive work is
already done — that is the anti-pattern the post identifies (a late handoff,
after the lead has already done the costly part solo).

## Step 2 — write ONE spec-quality brief

The brief is the whole game. The post's example: the lead that specified
*"operator() must be O(1) in pointer length: NO full token scan"* scored 94;
the lead that hand-coded it, forgot the constraint, and shipped linear time
scored 25. Specify constraints and outcomes, not keystrokes.

A good brief states, concretely:

- **The change**, in one or two sentences — the module that owns it, the
  smallest edit that solves it.
- **Constraints** — the house rules and edge cases this task will trip on. Name
  them so the sidekick doesn't rediscover them by failing a gate.
- **Definition of done, as artifacts** — *which test must pass, which gate must
  go green.* "test X asserts Y" beats "make it work." A gate is a checkable
  definition of done; use it as one.
- **What NOT to do** — the false paths recon revealed (don't add a helper file,
  reuse `existing_fn`, don't touch module Z).

Then delegate with the Task/subagent tool, `subagent_type: "sidekick"`, passing
the brief as the prompt. The sidekick runs on the cheap model set in its agent
file (`model: haiku` by default) — override per-call for purely mechanical work
if your tool supports it.

## Step 3 — review the diff cheaply, correct by re-briefing

When the sidekick reports back, review at lead prices only where review is
cheap — read the diff, not the whole tree:

- **Trust the gates it ran.** Don't pull its files back into your context and
  re-derive them. The post's cautionary case is the lead that distrusted good
  sidekick work, reverted it, and rewrote it by hand for no correctness gain —
  don't be that lead.
- **Read its "what I did NOT verify" section** — that's where the real risk is.
- **If review finds a real bug, prefer another cheap handoff** ("your test
  asserts the happy path but not the empty-input case — add it") over a
  lead-price rewrite. Rewrite yourself only when the fix genuinely needs
  context only you hold.

## Step 4 — close out through your own gates

The sidekick's local gates are not the merge bar. Before shipping, you (the
lead) still run the repo's full checks, review the whole diff, and integrate
(commit / PR / land) per this repo's process. Delegation changed who typed the
code; it did not move the definition of done.

## One-line honesty check

If you find yourself implementing before you've written a brief, or rewriting
the sidekick's diff instead of re-briefing it, you're the micromanager the post
warns about — stop and hand the judgment down.
