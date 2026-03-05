---
name: world-building
description: >
  Build the world before you write the story. Advanced context engineering for
  deep research, foundational research, and evidence-first planning before coding.
  Use when the user asks for world building, deep research before building,
  foundational research, market mapping before coding, hands-off but non-generic
  results, pillar mapping, folder scaffolding, parallel research, layered synthesis,
  or evidence-linked PRDs and build plans.
allowed-tools:
  - Read
  - Write
  - Edit
  - Grep
  - Glob
  - WebFetch
  - WebSearch
  - Task
license: MIT
---

# World Building for AI

Build the World before the product. This is the hands-off but non-generic path:

1. Context build
2. Synthesis
3. Build

Use this skill to turn a short prompt into a research system with explicit pillars, a tailored folder tree, parallel subagents, layered synthesis, critical review, and only then downstream specs or implementation plans.

## Start Here

Read these bundled references before doing substantial work:

1. `references/mode-selection.md` — choose the correct depth
2. `references/pillars.md` — map the smallest complete set of pillars
3. `references/folder-structure.md` — create the right scaffold
4. `references/orchestration.md` — run the research phases correctly
5. `references/five-sentence-superprompt.md` — use when the user wants the shortest shareable version

## Modes

Choose the lightest mode that still gives the user the right decisions:

- `foundational` — first-principles understanding and domain grounding
- `deep-research` — broad evidence gathering before a build
- `world-building` — the full harness: pillars, folders, research, synthesis, critique, and evidence-linked downstream docs

If the user says "build the world first", "deep research before building", or explicitly asks for a harness, default to `world-building`.

## Workflow

1. Restate the build target in one sentence. Infer low-risk missing details instead of stopping.
2. Choose the mode.
3. Create the scaffold:
   - `00-brief/vision.md`
   - `00-brief/world-pillars.md`
   - `00-brief/success-criteria.md`
   - tailored `01-research/`, `02-synthesis/`, `03-critical-review/`, `04-product/`, and `05-build/`
   - `WORLD.md` as the living source of truth
4. Define pillars:
   - purpose
   - priority
   - key questions
   - dependencies
   - output paths
5. Launch research:
   - run one lead researcher per pillar
   - use parallel subagents where the environment supports them
   - keep raw evidence in `01-research/`
6. Synthesize in layers:
   - per-pillar synthesis in `02-synthesis/per-tier/`
   - cross-synthesis, evidence map, and decision framework
   - critical review for contradictions, bias, implementation reality, missing voices, and failure patterns
7. Derive downstream deliverables only after synthesis:
   - PRD
   - UX flows
   - acceptance criteria
   - architecture
   - backlog
   - roadmap
   - strategy docs
8. Iterate:
   - update `WORLD.md` first
   - then synthesis
   - then product docs
   - then code or execution plans

## Operating Rules

- Prefer breadth first. Narrow only after evidence exists.
- Use current sources when facts are unstable.
- Mark assumptions, contradictions, and uncertainty explicitly.
- Separate raw evidence, synthesis, critique, and decisions.
- Treat marketing claims as untrusted until corroborated.
- Include contrary evidence and failure cases, not just success stories.
- If `Task` is unavailable, preserve the same phases sequentially instead of skipping them.
- Tailor the folder tree and pillar set to the request. This is a system, not a rigid checklist.

## Research File Contract

Use this structure for each lead research file unless the task clearly needs a variant:

```markdown
# [Topic] Research Report

## Executive Summary
- Key finding
- Confidence
- Product or strategy implications
- Research gaps

## Detailed Findings

## Sub-Agent Requests

## Design Implications

## Sources
```

## Minimum Deliverables

- `WORLD.md`
- `00-brief/world-pillars.md`
- structured `01-research/` outputs for each active pillar
- `02-synthesis/cross-synthesis.md`
- `02-synthesis/evidence-map.md`
- `02-synthesis/decision-framework.md`
- at least one evidence-linked downstream deliverable in `04-product/`

## Trigger Phrases

Treat all of these as strong signals to use this skill:

- "deep research"
- "foundational research"
- "world building"
- "deep research before building"
- "build the world first"
- "research the whole space before we code"
- "hands-off but non-generic"
