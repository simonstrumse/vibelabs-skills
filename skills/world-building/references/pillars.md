# Pillar Design

Define the smallest complete world the project depends on.

## Default Pillar Families

Start here, then merge, rename, split, or cut:

1. Problem and outcomes
2. User segments and jobs-to-be-done
3. Foundations, science, or first principles
4. Competitive landscape and substitutes
5. Technical architecture and feasibility
6. Integrations and ecosystem
7. UX, workflow, and interaction patterns
8. Market, positioning, and timing
9. Business model and monetization
10. Infrastructure, operations, and cost
11. Legal, compliance, and risk
12. Existing assets, data, and leverage

## Priority Model

- `P0` — blocks architecture or direction decisions
- `P1` — shapes scope, workflow, or positioning
- `P2` — refinement, mitigation, or later-stage optimization

Only a few pillars should be `P0`.

## Pillar Definition Contract

For each pillar, write:

```markdown
### [Pillar Name]
**Description**: What this pillar covers
**Priority**: P0 | P1 | P2
**Key Questions**:
- Question 1
- Question 2
- Question 3
**Dependencies**: Which pillars feed this one
**Output Paths**:
- 01-research/...
- 02-synthesis/...
```

## Customization Rules

- Add pillars only when the project has a real dependency not covered by the defaults.
- Remove pillars that do not materially affect decisions.
- Split a pillar only when it would be too broad for one lead researcher.
- Keep the list short enough that each pillar produces a distinct decision surface.
