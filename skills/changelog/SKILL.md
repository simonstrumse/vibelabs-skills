---
name: changelog
description: Read or update project changelog. Use PROACTIVELY whenever you need context about what was done previously in ANY area of the codebase, and after making changes or decisions worth preserving. This is your strategic memory - use it liberally to avoid redoing work or missing context.
allowed-tools:
  - Read
  - Write
  - Edit
  - Glob
  - Bash
---

# Changelog Management Skill

You have been invoked to manage the project changelog. This is a **strategic memory system**, not a tool log.

## Your Tasks

### If Reading (gathering context):

1. **Identify the relevant changelog:**
   - Project root → `./CHANGELOG.md`
   - Monorepo apps → `apps/<app-name>/CHANGELOG.md`
   - Sub-packages → `packages/<pkg-name>/CHANGELOG.md`

2. **Read the Current State section** (top of file) for quick orientation

3. **Search recent History entries** for:
   - The specific area/files being modified
   - Related decisions or patterns
   - Known gotchas or technical notes

4. **Report back** with relevant context that should inform the current work

### If Writing (documenting changes):

1. **Determine what happened this session:**
   - Code changes made
   - Decisions reached
   - Direction shifts discussed
   - Insights generated
   - Technical gotchas discovered

2. **Update the Current State section** if capabilities changed:
   - Update progress indicators
   - Add/check items in "What's Working"
   - Update "What's Next" priorities

3. **Add a History entry** with:
   ```markdown
   ## [DATE] - [BRIEF SESSION SUMMARY]

   ### Direction & Vision
   - [Strategic shifts, even without code]

   ### Changes
   - **[Description]** — `[files]` ([context])

   ### Insights
   - [★ Insight blocks or learnings]

   ### Technical Notes
   - [Platform quirks, decisions]

   ### Pending
   - [ ] [Incomplete items]
   ```

4. **Be specific**, not vague:
   - ❌ "Fixed auth bug"
   - ✅ "Fixed middleware setting response headers instead of request headers — server components read request headers"

## Bootstrap Mode (First-Time Setup)

**If no CHANGELOG.md exists, bootstrap from session history:**

### Step 1: Find the sessions index

The project path determines the sessions folder. Convert the project path to the folder name:
- `/path/to/your/project`
- becomes: `~/.claude/projects/-path-to-your-project/`

Read the `sessions-index.json` file in that folder.

### Step 2: Extract session summaries

The index contains entries like:
```json
{
  "sessionId": "abc123",
  "summary": "Human-readable session summary",
  "firstPrompt": "What user first asked",
  "created": "2026-01-14T20:59:20.530Z",
  "gitBranch": "feat/some-feature",
  "messageCount": 10
}
```

**CRITICAL: Only use the `summary` field - these are factual, already generated. DO NOT invent, deduce, or hallucinate additional details.**

### Step 3: Create changelog from summaries

Group sessions by date and create entries:

```markdown
## Historical Sessions (from Claude Code session logs)

> Note: These entries are auto-generated from session summaries.
> Context may be incomplete. Add details if you remember more.

### 2026-01-14
- **Vibelabs Bento Grid Hero & Site Revert** (branch: `feat/homepage-redesign-2026`, 7 messages)
- **Human-Centric Vibelabs One-Pager** (branch: `main`, 10 messages)

### 2026-01-05
- **Per-app volume mixer macOS app research** (branch: `main`, 7 messages)
```

### Step 4: Add Current State section

After historical entries, add a Current State section based on:
- Git status (`git log --oneline -10`)
- README content
- Visible project structure

**Mark inferred information clearly:**
```markdown
## Current State

> **Last Updated:** [DATE]
> **Status:** *Inferred from codebase - please verify*

### What's Working (inferred from code)
- [x] Item visible in codebase
```

### Bootstrap Rules

1. **ONLY use factual sources**: session summaries, git log, existing docs
2. **NEVER invent context**: If you don't know WHY something was done, say "Context unknown"
3. **NEVER hallucinate details**: Session summary says "Fixed auth bug" → write exactly that, don't elaborate
4. **Mark uncertainty**: Use "inferred", "from session log", "context unknown" labels
5. **Invite corrections**: Add note asking user to fill in missing context

---

## Context Triggers

Automatically consider **reading** the changelog when:
- About to modify ANY code you haven't touched recently in this session
- Wondering "what was the approach here?" or "why is this done this way?"
- User asks about previous work ("what did we do with X?", "where were we?")
- Starting a new session or after context compaction
- Before making changes that might conflict with recent decisions
- Unsure if something was already implemented or fixed

Automatically consider **writing** when:
- ANY code changes were made (features, fixes, refactoring)
- Decisions were reached about how to do something
- User expressed preferences, changed direction, or clarified vision
- `★ Insight` blocks were generated
- Something non-obvious was discovered (gotchas, patterns, behaviors)
- Leaving work incomplete (document what's pending)
- A conversation led to strategic clarity (even without code changes)
