---
name: ralph
description: Plan a feature end-to-end for the Ralph autonomous agent loop. Use when starting a new feature, writing requirements, drafting a PRD, or producing prd.json. Generates a markdown PRD from a feature description, then converts it to the prd.json format Ralph executes one story at a time.
user-invocable: true
---

# RALPH

End-to-end planning skill for the Ralph autonomous agent loop. Takes a feature idea and produces two artifacts:

1. **`tasks/prd-[feature-name].md`** — a structured Product Requirements Document
2. **`prd.json`** — the same plan, sized and ordered for one-story-per-iteration execution by Ralph

Run both phases together, or just one. Phase 1 alone is useful when you only want a PRD. Phase 2 alone is useful when a PRD already exists and you need to wire it up to Ralph.

The autonomous agent prompt that consumes `prd.json` lives in [`agent-instructions.md`](agent-instructions.md). An example output is in [`prd.json.example`](prd.json.example).

---

## When to Run Each Phase

| You have… | Run |
|---|---|
| A feature idea, no PRD | Phase 1 → Phase 2 |
| A PRD (markdown or pasted text) | Phase 2 only |
| A PRD that needs revision | Phase 1 (refine) → Phase 2 |

Ask the user which they want if it is ambiguous.

---

# Phase 1 — Generate the PRD

Receive a feature description and produce a markdown PRD at `tasks/prd-[feature-name].md`.

**Do NOT start implementing in this phase. Just produce the PRD.**

## Step 1.1 — Clarifying Questions

Ask 3–5 essential questions only where the initial prompt is ambiguous. Use lettered options so the user can answer fast (e.g. "1A, 2C, 3B").

Cover:

- **Problem / Goal** — what does this solve?
- **Core Functionality** — what are the key actions?
- **Scope / Boundaries** — what should it explicitly NOT do?
- **Success Criteria** — how do we know it is done?

### Format

```
1. What is the primary goal of this feature?
   A. Improve user onboarding experience
   B. Increase user retention
   C. Reduce support burden
   D. Other: [please specify]

2. Who is the target user?
   A. New users only
   B. Existing users only
   C. All users
   D. Admin users only

3. What is the scope?
   A. Minimal viable version
   B. Full-featured implementation
   C. Just the backend / API
   D. Just the UI
```

## Step 1.2 — PRD Structure

Generate the PRD with these sections:

### 1. Introduction / Overview
Brief description of the feature and the problem it solves.

### 2. Goals
Specific, measurable objectives (bullet list).

### 3. User Stories
Each story:

- **Title** — short descriptive name
- **Description** — "As a [user], I want [feature] so that [benefit]"
- **Acceptance Criteria** — verifiable checklist of what "done" means

Each story must be implementable in one focused session.

```markdown
### US-001: [Title]
**Description:** As a [user], I want [feature] so that [benefit].

**Acceptance Criteria:**
- [ ] Specific verifiable criterion
- [ ] Another criterion
- [ ] Typecheck / lint passes
- [ ] **[UI stories only]** Verify in browser
```

Acceptance criteria must be verifiable, not vague. "Works correctly" is bad. "Button shows confirmation dialog before deleting" is good. UI stories must always include "Verify in browser."

### 4. Functional Requirements
Numbered list:
- "FR-1: The system must allow users to…"
- "FR-2: When a user clicks X, the system must…"

### 5. Non-Goals (Out of Scope)
What this feature will NOT include. Critical for managing scope.

### 6. Design Considerations (Optional)
UI / UX requirements, mockup links, components to reuse.

### 7. Technical Considerations (Optional)
Constraints, dependencies, integration points, performance requirements.

### 8. Success Metrics
How success will be measured.

### 9. Open Questions
Remaining questions or areas needing clarification.

## Step 1.3 — Output

- **Format**: Markdown (`.md`)
- **Location**: `tasks/`
- **Filename**: `prd-[feature-name].md` (kebab-case)

## Phase 1 Checklist

- [ ] Asked clarifying questions with lettered options
- [ ] Incorporated user answers
- [ ] User stories are small and specific
- [ ] Functional requirements are numbered and unambiguous
- [ ] Non-goals section defines clear boundaries
- [ ] Saved to `tasks/prd-[feature-name].md`

---

# Phase 2 — Convert the PRD to `prd.json`

Take a PRD (the file from Phase 1, or a markdown / pasted PRD the user already has) and emit `prd.json` to the project root (or a user-specified location).

## Output Format

```json
{
  "project": "[Project Name]",
  "branchName": "ralph/[feature-name-kebab-case]",
  "description": "[Feature description from PRD title / intro]",
  "userStories": [
    {
      "id": "US-001",
      "title": "[Story title]",
      "description": "As a [user], I want [feature] so that [benefit]",
      "acceptanceCriteria": [
        "Criterion 1",
        "Criterion 2",
        "Typecheck passes"
      ],
      "priority": 1,
      "passes": false,
      "notes": ""
    }
  ]
}
```

## Story Size: The Number One Rule

**Each story must fit inside ONE iteration (one context window).**

Ralph spawns a fresh agent per iteration with no memory of previous work. Oversized stories run out of context and produce broken code.

### Right-sized
- Add a database column and migration
- Add a UI component to an existing page
- Update a server action with new logic
- Add a filter dropdown to a list

### Too big — split
- "Build the entire dashboard" → schema, queries, UI components, filters
- "Add authentication" → schema, middleware, login UI, session handling
- "Refactor the API" → one story per endpoint or pattern

**Rule of thumb:** if you cannot describe the change in 2–3 sentences, it is too big.

## Story Ordering: Dependencies First

Stories execute in priority order. Earlier stories must not depend on later ones.

1. Schema / database changes (migrations)
2. Server actions / backend logic
3. UI components that consume the backend
4. Dashboard / summary views that aggregate data

## Acceptance Criteria: Must Be Verifiable

Each criterion must be something the agent can CHECK.

### Good
- "Add `status` column to tasks table with default 'pending'"
- "Filter dropdown has options: All, Active, Completed"
- "Clicking delete shows confirmation dialog"
- "Typecheck passes"
- "Tests pass"

### Bad
- "Works correctly"
- "User can do X easily"
- "Good UX"
- "Handles edge cases"

### Always add as final criterion
```
"Typecheck passes"
```

For stories with testable logic, also include:
```
"Tests pass"
```

For stories that change UI, also include:
```
"Verify in browser"
```

Frontend stories are NOT complete until visually verified.

## Conversion Rules

1. Each user story in the PRD becomes one JSON entry
2. IDs are sequential — `US-001`, `US-002`, …
3. `priority` reflects dependency order, then document order
4. All stories start with `passes: false` and empty `notes`
5. `branchName` is derived from the feature name, kebab-case, prefixed `ralph/`
6. Always append `"Typecheck passes"` to every story's acceptance criteria

## Splitting Large PRDs

**Original:**
> "Add user notification system"

**Split:**
1. US-001: Add notifications table to database
2. US-002: Create notification service for sending notifications
3. US-003: Add notification bell icon to header
4. US-004: Create notification dropdown panel
5. US-005: Add mark-as-read functionality
6. US-006: Add notification preferences page

## Example

**Input PRD:**
```markdown
# Task Status Feature

Add ability to mark tasks with different statuses.

## Requirements
- Toggle between pending / in-progress / done on task list
- Filter list by status
- Show status badge on each task
- Persist status in database
```

**Output `prd.json`:**
```json
{
  "project": "TaskApp",
  "branchName": "ralph/task-status",
  "description": "Task Status Feature - Track task progress with status indicators",
  "userStories": [
    {
      "id": "US-001",
      "title": "Add status field to tasks table",
      "description": "As a developer, I need to store task status in the database.",
      "acceptanceCriteria": [
        "Add status column: 'pending' | 'in_progress' | 'done' (default 'pending')",
        "Generate and run migration successfully",
        "Typecheck passes"
      ],
      "priority": 1,
      "passes": false,
      "notes": ""
    },
    {
      "id": "US-002",
      "title": "Display status badge on task cards",
      "description": "As a user, I want to see task status at a glance.",
      "acceptanceCriteria": [
        "Each task card shows colored status badge",
        "Badge colors: gray=pending, blue=in_progress, green=done",
        "Typecheck passes",
        "Verify in browser"
      ],
      "priority": 2,
      "passes": false,
      "notes": ""
    },
    {
      "id": "US-003",
      "title": "Add status toggle to task list rows",
      "description": "As a user, I want to change task status directly from the list.",
      "acceptanceCriteria": [
        "Each row has status dropdown or toggle",
        "Changing status saves immediately",
        "UI updates without page refresh",
        "Typecheck passes",
        "Verify in browser"
      ],
      "priority": 3,
      "passes": false,
      "notes": ""
    },
    {
      "id": "US-004",
      "title": "Filter tasks by status",
      "description": "As a user, I want to filter the list to see only certain statuses.",
      "acceptanceCriteria": [
        "Filter dropdown: All | Pending | In Progress | Done",
        "Filter persists in URL params",
        "Typecheck passes",
        "Verify in browser"
      ],
      "priority": 4,
      "passes": false,
      "notes": ""
    }
  ]
}
```

## Archiving Previous Runs

Before writing a new `prd.json`, check if there is an existing one from a different feature:

1. Read the current `prd.json` if it exists
2. Compare `branchName` against the new feature's
3. If different:
   - Create archive folder `archive/YYYY-MM-DD-feature-name/`
   - Copy current `prd.json` and `progress.txt` into it
   - Reset `progress.txt` with a fresh header

## Phase 2 Checklist

- [ ] Previous run archived (if `prd.json` exists with a different `branchName`)
- [ ] Each story fits in one iteration
- [ ] Stories are ordered by dependency (schema → backend → UI)
- [ ] Every story has `"Typecheck passes"` as a criterion
- [ ] UI stories have `"Verify in browser"`
- [ ] Acceptance criteria are verifiable, not vague
- [ ] No story depends on a later story

---

# Running the Loop

Once `prd.json` is in place, point Ralph at it with the prompt in [`agent-instructions.md`](agent-instructions.md). Each iteration:

1. Reads `prd.json` and `progress.txt`
2. Picks the highest-priority `passes: false` story
3. Implements, runs quality checks, commits
4. Marks `passes: true` and appends to `progress.txt`

Ralph stops automatically when every story is `passes: true`.
