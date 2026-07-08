# claudemd-starter

The starter file from Scaler's video *"You've Been Using Claude Code Wrong (Do This Instead)."* This is the CLAUDE.md / AGENTS.md layout the video walks through — shaped around a Spring Boot service as its worked example. It's a set of files to fork and adapt, not a runnable app. Fork it, strip our specifics, and use it as the starting shape for your own repo.

## What's here

| File | What it is | Principle it embodies |
|---|---|---|
| `CLAUDE.md` | Short. Claude-Code-specific extras. Imports `AGENTS.md` | Anthropic memory-doc convergence statement — *"create a CLAUDE.md that imports AGENTS.md"* |
| `AGENTS.md` | Tool-agnostic spine: commands, structure, conventions, rules | Operations theory (Anthropic doc + Mitchell Hashimoto's Ghostty). ~55 lines |
| `LESSONS.md` | The failure trace. Every rule in `AGENTS.md` has an entry here | Mitchell's standard — *every line traces to a specific named failure* |
| `.claude/rules/api-handlers.md` | Path-scoped rules for `api/**` | Anthropic memory-doc Section on path-scoped rules — scale beyond CLAUDE.md without bloating it |
| `.claude/skills/spring-test-slice-chooser/` | Lazy expertise. Loaded on demand when relevant | The 4-layer architecture, layer 2 — only-sometimes content lives here, not in the always-loaded file |
| `.claude/hooks/` (empty) | Deterministic guarantees | Layer 4 — escalate a rule here when advisory pressure isn't enough |
| `.claude/agents/` (empty) | Subagents with isolated context | Layer 3 — keep large file-reading or log-analysis out of the main session |

## What's NOT here (intentionally)

These are the exclusions from Anthropic's published best-practices doc applied strictly:

- **No "Java conventions" section.** Claude knows Java.
- **No "write tests / be concise / clean code" lines.** That's the *self-evident* column. Cut.
- **No agent-character meta-rules** ("think before coding," "state your assumptions"). That's the Karpathy/Chang theory — a different shape entirely. The video distinguishes the two; this starter is operations-theory.
- **No file-by-file codebase description.** The structure section gives the layers; the agent can read the code.
- **No detailed API documentation.** Link to docs.

## The lineage

Each design choice maps back to a specific voice in the corpus the video surveys:

- **Sub-200 lines, lead with commands** → Anthropic best-practices doc + memory doc.
- **Operations over character** → Anthropic's own published files (cookbooks, agent-sdk, code-base-action, quickstarts) + Mitchell's Ghostty AGENTS.md.
- **Every rule traces to a failure** → Mitchell Hashimoto (Pragmatic Engineer interview, Ghostty repo).
- **Path-scoped rules + skills + hooks** → Anthropic memory doc, four-layer architecture.
- **CLAUDE.md imports AGENTS.md** → Anthropic memory doc convergence statement.
- **The personal CLAUDE.md is a redirect** → Boris Cherny (Threads.com `@boris_cherny`).
- **LESSONS.md as the long-form companion** → Dan Shipper (*Compounding Engineering*) + Boris's *"anytime we see Claude do something incorrectly we add it"* practice.

## How to start

1. Clone this repo as the seed for your service.
2. Rename `co.scaler.demo` to your package; replace the domain.
3. **Empty `LESSONS.md` and the `Rules` section in `AGENTS.md`.** Don't inherit our failures — write your own as they happen. Inherited rules without their original failures rot fast.
4. Keep:
    - The `CLAUDE.md` → `AGENTS.md` import.
    - The `LESSONS.md` discipline (every `Rules` line has an `L<n>` entry).
    - The four `.claude/` subdirectories — start them empty if you want, but the slots are reserved.
5. The first time you correct Claude on the same thing twice — open `LESSONS.md`, write the entry, add the rule.

## The two-line personal pattern

For each engineer cloning this repo, your `~/.claude/CLAUDE.md` should look approximately like:

```
For projects under ~/code/your-org, see ./CLAUDE.md.
Otherwise, vanilla.
```

The team file is where the work compounds. A personal rule book maintained in parallel splits the constitution and stops the compounding. Don't.

## The video

V5 walks through this layout on camera, section by section, with the lineage on screen. This README is the still version of that walk. The video link will appear here once published.
