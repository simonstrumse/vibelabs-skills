# Folder Structure

Create a scaffold that matches the active pillars. Do not blindly create irrelevant folders.

## Core Structure

```text
[project]/
├── 00-brief/
│   ├── vision.md
│   ├── world-pillars.md
│   └── success-criteria.md
├── 01-research/
│   ├── [pillar-a]/
│   ├── [pillar-b]/
│   └── [pillar-c]/
├── 02-synthesis/
│   ├── per-tier/
│   ├── cross-synthesis.md
│   ├── evidence-map.md
│   └── decision-framework.md
├── 03-critical-review/
│   ├── contradictions.md
│   ├── bias-detection.md
│   ├── implementation-reality.md
│   └── gaps-identified.md
├── 04-product/
│   ├── PRD.md
│   ├── ARCHITECTURE.md
│   ├── BACKLOG.md
│   └── RISKS_AND_MITIGATIONS.md
├── 05-build/
├── WORLD.md
└── CHANGELOG.md
```

## Recommended Research Subtrees

Create only the subtrees that fit the chosen pillars:

- `01-research/foundations/`
- `01-research/users/`
- `01-research/competitive/`
- `01-research/technical/`
- `01-research/integrations/`
- `01-research/ux/`
- `01-research/market/`
- `01-research/business/`
- `01-research/infrastructure/`
- `01-research/legal/`
- `01-research/assets/`

## Optional Extensions

Add these only when the user wants a fuller operating system around the product:

```text
06-brand/
07-marketing/
08-business/
09-legal/
```

## Minimum First Files

Create these before launching research:

- `00-brief/vision.md`
- `00-brief/world-pillars.md`
- `00-brief/success-criteria.md`
- `WORLD.md`
