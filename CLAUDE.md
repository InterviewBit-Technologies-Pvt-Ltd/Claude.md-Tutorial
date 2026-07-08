# CLAUDE.md

@AGENTS.md

## Claude Code specifics

- Use plan mode (Shift+Tab twice) for non-trivial work — anything touching 3+ files or 50+ lines. For typos, renames, and one-line fixes, skip the plan.
- For "which test annotation should I use here?" — invoke the `spring-test-slice-chooser` skill.
- Before adding a rule to `AGENTS.md`, check `LESSONS.md`. If you can't name the failure the rule prevents, it doesn't belong in the rules section.

## Your personal CLAUDE.md

If you're cloning this repo to your machine, your `~/.claude/CLAUDE.md` should be a redirect — not a parallel rule book:

```
# Personal CLAUDE.md
For projects under ~/code/scaler-demo, see ./CLAUDE.md.
Otherwise, vanilla.
```

The team file is where the work compounds. Don't carry a personal CLAUDE.md you maintain in parallel; the file you're reading right now is the one that should compound.
