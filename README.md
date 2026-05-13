# BioRouter Skills

A collection of **skills** for [BioRouter](https://github.com/BaranziniLab/BioRouter) — UCSF's local-first AI workspace for biomedical research.

Skills are reusable instruction bundles that BioRouter loads on demand to shape how the model writes code, drafts documents, and reasons about a problem. Some run automatically when relevant files are touched (style enforcement, language conventions). Others are *user-invocable* and respond to a slash command like `/ralph` or `/anti-ai-writing` in the chat panel.

This repository hosts the skills curated by the [Baranzini Lab](https://baranzinilab.ucsf.edu/) at UCSF. Each top-level folder is a single skill (or, for `superpowers/`, a bundled collection of sub-skills).

---

## Installing a skill

1. Download the skill's `.zip` from the [Releases](https://github.com/BaranziniLab/biorouter-skills/releases) page.
2. Unzip it.
3. Move the resulting folder into `~/.config/biorouter/skills/`.
4. Open BioRouter. The skill appears under **Skills** automatically. User-invocable skills respond to `/skill-name`.

You can also clone individual folders directly into `~/.config/biorouter/skills/` if you prefer to track updates with `git`.

---

## Skills in this repo

| Skill | Trigger | What it does |
|---|---|---|
| [`anti-ai-writing`](anti-ai-writing) | `/anti-ai-writing` | Pass/fail checklist that strips AI tells out of prose, articles, and essays. |
| [`develop-biorouter-extension`](develop-biorouter-extension) | `/develop-biorouter-extension` | Step-by-step guide for building a `.brxt` extension — manifest, MCP server, bundled skills. |
| [`ggplot-visualization`](ggplot-visualization) | Auto (R plotting) | Applies the lab's ggplot2 style automatically whenever R plotting code is written. |
| [`python-scripting`](python-scripting) | Auto (Python) | Enforces Python naming, typing, error handling, and project-structure conventions. |
| [`r-scripting`](r-scripting) | Auto (R) | Tidyverse conventions and documentation standards for R. |
| [`ralph`](ralph) | `/ralph` | End-to-end planning for the **Ralph** autonomous agent loop — generates a markdown PRD from a feature idea, then converts it to `prd.json` sized for one-story-per-iteration execution. |
| [`superpowers`](superpowers) | Mixed (see folder) | A bundled collection of 13 engineering-discipline skills (brainstorming, TDD, systematic debugging, parallel agents, plan writing, code review, git worktrees, more). |

---

## Repository layout

```
biorouter-skills/
├── anti-ai-writing/
│   └── SKILL.md
├── develop-biorouter-extension/
│   └── SKILL.md
├── ggplot-visualization/
│   └── SKILL.md
├── python-scripting/
│   └── SKILL.md
├── r-scripting/
│   └── SKILL.md
├── ralph/
│   ├── SKILL.md                  ← the skill itself
│   ├── agent-instructions.md     ← prompt for the autonomous loop
│   └── prd.json.example
└── superpowers/
    ├── brainstorming/
    ├── test-driven-development/
    ├── systematic-debugging/
    └── … (10 more)
```

Every skill folder contains a `SKILL.md` with YAML frontmatter (`name`, `description`, optional `user-invocable`). Anything else in the folder is supporting material the skill can reference.

---

## Contributing a new skill

1. Fork this repo.
2. Add a new top-level folder named after your skill (kebab-case).
3. Drop a `SKILL.md` into it with frontmatter:

   ```markdown
   ---
   name: my-skill
   description: One sentence — what it does and when to use it. This is the line the model reads to decide whether to load the skill.
   user-invocable: true   # optional
   ---

   # My Skill

   The skill body. Be concrete, give examples, and keep it focused on one job.
   ```

4. Open a pull request. A maintainer will review and cut a release with a matching `.zip` so BAAM picks it up automatically.

A good `description:` is the single most important line — it is what the model sees when deciding whether to invoke the skill, so be specific about *when* to use it, not just *what* it does.

---

## License & attribution

Skills here are licensed under Apache 2.0, matching the BioRouter project. Some skills (the `superpowers/` collection, `anti-ai-writing`) are adapted from upstream open-source skill libraries; see each skill's frontmatter for attribution where applicable.

Maintained by the [Baranzini Lab](https://baranzinilab.ucsf.edu/) at UCSF.
