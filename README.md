# cc-orchestrate

Make Claude Code delegate. Your main model plans and reviews, a cheaper sidekick does the implementation. Two files, no install.

## Setup

Paste this into Claude Code:

```
Set up the cc-orchestrate kit here: clone https://github.com/zidankazi/cc-orchestrate
to a temp dir, copy its .claude/agents/sidekick.md and .claude/skills/orchestrate/
into my .claude/ (make the dirs if needed), then delete the clone.
```

## Use

Run `/orchestrate` and say what you want. The lead pokes around, writes a brief, hands it off, checks the diff.

## Models

- Lead: your main model (`/model`). For the best results, pick Fable 5.
- Sidekick: Sonnet by default, set in `sidekick.md`. Doing something simple? Drop it to Haiku.

Not everything's worth handing off. Genuinely difficult debugging and judgment calls stay with the lead, and the skill knows the difference.

## Watch it run

[roster](https://github.com/zidankazi/roster) shows you both agents live: who's stuck, who's working, who's done.

MIT.
