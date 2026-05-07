# Ralph Agent Instructions

Use this as the prompt when running BioRouter as an autonomous Ralph iteration agent.
The agent reads `prd.json`, implements the next incomplete story, and updates progress.

---

## Your Task

1. Read `prd.json` in the project root
2. Read `progress.txt` (check the Codebase Patterns section first)
3. Verify you are on the correct branch from the PRD's `branchName`. If not, check it out or create from main.
4. Pick the **highest priority** user story where `passes: false`
5. Implement that single user story
6. Run quality checks (typecheck, lint, tests — use whatever the project requires)
7. Update `AGENTS.md` files if you discover reusable patterns (see below)
8. If checks pass, commit ALL changes with message: `feat: [Story ID] - [Story Title]`
9. Update `prd.json` to set `passes: true` for the completed story
10. Append your progress to `progress.txt`

---

## Progress Report Format

APPEND to `progress.txt` (never replace, always append):

```
## [Date/Time] - [Story ID]
- What was implemented
- Files changed
- **Learnings for future iterations:**
  - Patterns discovered (e.g., "this codebase uses X for Y")
  - Gotchas encountered (e.g., "don't forget to update Z when changing W")
  - Useful context (e.g., "the evaluation panel is in component X")
---
```

The learnings section is critical — it helps future iterations avoid repeating mistakes and understand the codebase better.

---

## Consolidate Patterns

If you discover a **reusable pattern** that future iterations should know, add it to the `## Codebase Patterns` section at the TOP of `progress.txt` (create it if it doesn't exist):

```
## Codebase Patterns
- Use `sql<number>` template for aggregations
- Always use `IF NOT EXISTS` for migrations
- Export types from actions.ts for UI components
```

Only add patterns that are **general and reusable**, not story-specific details.

---

## Update AGENTS.md Files

Before committing, check if any edited files have learnings worth preserving in nearby `AGENTS.md` files:

1. **Identify directories with edited files**
2. **Check for existing AGENTS.md** in those directories or parent directories
3. **Add valuable learnings** if you discovered something future developers/agents should know:
   - API patterns or conventions specific to that module
   - Gotchas or non-obvious requirements
   - Dependencies between files
   - Testing approaches for that area

**Examples of good AGENTS.md additions:**
- "When modifying X, also update Y to keep them in sync"
- "This module uses pattern Z for all API calls"
- "Tests require the dev server running on PORT 3000"

**Do NOT add:** Story-specific implementation details, temporary debugging notes, information already in `progress.txt`.

---

## Quality Requirements

- ALL commits must pass the project's quality checks (typecheck, lint, test)
- Do NOT commit broken code
- Keep changes focused and minimal
- Follow existing code patterns

---

## Browser Verification (Required for Frontend Stories)

For any story with "Verify in browser" in its acceptance criteria:

1. Start the development server if not already running
2. Use BioRouter's computer controller to navigate to the relevant page
3. Verify the UI changes work as expected
4. Capture a screenshot if helpful for the progress log

A frontend story is NOT complete until browser verification passes.

---

## Stop Condition

After completing a user story, check if ALL stories have `passes: true`.

- **If all stories are complete:** Report "All stories complete. Ralph loop finished." and stop.
- **If stories remain:** End your response normally — the next iteration will pick up the next story.

---

## Important

- Work on **ONE story per iteration**
- Commit frequently
- Keep CI green
- Read the Codebase Patterns section in `progress.txt` before starting
