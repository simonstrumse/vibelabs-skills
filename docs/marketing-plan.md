# Vibelabs Skills — Marketing & Distribution Plan

> Last updated: 2026-02-21
> Repo: https://github.com/simonstrumse/vibelabs-skills

---

## Ecosystem Overview

The agent skills ecosystem (as of February 2026) has three layers:

| Layer | What it is | How to distribute |
|-------|-----------|-------------------|
| **Skills** | Single `SKILL.md` files following the [Agent Skills spec](https://agentskills.io/specification) | Copy to `~/.claude/skills/` or install via `npx skills add` |
| **Plugins** | Bundles with `.claude-plugin/plugin.json` manifest | `claude plugin add owner/repo` |
| **Marketplaces** | Registries with `marketplace.json` listing multiple plugins | `/plugin marketplace add owner/repo` |

Our repo is structured as a **Plugin** (layer 2) — installable with a single command.

### Compatible agents

The Agent Skills spec is supported by 25+ tools:
- Claude Code
- GitHub Copilot (Codex CLI)
- Cursor
- Gemini CLI
- Cline
- And 18+ others listed on agentskills.io

---

## Discovery Platforms

### Tier 1: Automatic (install-driven)

| Platform | URL | How it works | Status |
|----------|-----|-------------|--------|
| **skills.sh** | [skills.sh](https://skills.sh/) | Automatic via `npx skills add` telemetry. Higher installs = higher ranking. 70,000+ skills indexed. | NOT YET — need installs |
| **SkillsMP** | [skillsmp.com](https://skillsmp.com) | Auto-scrapes GitHub repos with 2+ stars. | NOT YET — need 2+ stars |

### Tier 2: Submission required

| Platform | URL | How to submit | Review? | Status |
|----------|-----|--------------|---------|--------|
| **Anthropic Official Directory** | [anthropics/claude-plugins-official](https://github.com/anthropics/claude-plugins-official) | [clau.de/plugin-directory-submission](https://clau.de/plugin-directory-submission) | Yes (quality + security) | TODO |
| **Skills Directory** | [skillsdirectory.com](https://www.skillsdirectory.com/) | `/submit` page | Yes (security scan, 50+ rules) | TODO |
| **claude-plugins.dev** | [claude-plugins.dev](https://claude-plugins.dev/) | PR to [Kamalnrf/claude-plugins](https://github.com/Kamalnrf/claude-plugins) | Community review | TODO |
| **SkillUse** | [github.com/skilluse/skilluse](https://github.com/skilluse/skilluse) | `skilluse publish <skill-name>` | No | TODO |

### Tier 3: Awesome lists (PR required)

| List | Stars | URL | Status |
|------|-------|-----|--------|
| VoltAgent/awesome-agent-skills | 7.6k | [GitHub](https://github.com/VoltAgent/awesome-agent-skills) | TODO |
| travisvn/awesome-claude-skills | — | [GitHub](https://github.com/travisvn/awesome-claude-skills) | TODO |
| ComposioHQ/awesome-claude-skills | — | [GitHub](https://github.com/ComposioHQ/awesome-claude-skills) | TODO |
| hesreallyhim/awesome-claude-code | — | [GitHub](https://github.com/hesreallyhim/awesome-claude-code) | TODO |
| abubakarsiddik31/claude-skills-collection | — | [GitHub](https://github.com/abubakarsiddik31/claude-skills-collection) | TODO |

---

## GitHub Topics (already applied)

These topics are set on the repo for search discovery:
- `claude-code-skills`
- `agent-skills`
- `claude-code`
- `claude-ai`
- `claude-code-plugin`
- `claude-skills`
- `ai-skills`
- `ios-development`
- `swiftui`
- `liquid-glass`

---

## Marketing Actions

### Phase 1: Foundations (this week)

- [x] Create public repo with 12 skills
- [x] Structure as Claude Code plugin with `plugin.json`
- [x] Add GitHub topics for discovery
- [x] Send internal notification (emma@vibelabs.no)
- [ ] Submit to Anthropic official directory (clau.de/plugin-directory-submission)
- [ ] Submit to skillsdirectory.com for security verification badge
- [ ] PR to claude-plugins.dev (Kamalnrf/claude-plugins)

### Phase 2: Community (first week)

- [ ] Reddit r/ClaudeCode post — install instructions + top 3 skill highlights
- [ ] Reddit r/ClaudeAI post — cross-post with focus on research + iOS skills
- [ ] Twitter/X thread — "12 skills from real-world projects" with before/after examples
- [ ] LinkedIn post — open-source announcement, tag Anthropic
- [ ] Submit PRs to all 5 awesome-lists above

### Phase 3: Content (first month)

- [ ] Blog post: "How we built production-tested agent skills" — deep dive on fiken gotchas or parallel-research methodology
- [ ] Blog post: "The Agent Skills Ecosystem in 2026" — landscape analysis based on our research
- [ ] Create demo GIFs showing skills in action (parallel-research, fiken invoice flow)
- [ ] Add GIFs/screenshots to repo README

### Phase 4: Ongoing

- [ ] Get 2+ GitHub stars so SkillsMP scraper picks us up
- [ ] Drive `npx skills add` installs to climb skills.sh leaderboard
- [ ] Monitor skills.sh trending for positioning opportunities
- [ ] Add new skills as we build them (target: 20 skills)
- [ ] Consider premium tier for specialized skills (market exists: $199 for 10 skills)

---

## Competitive Landscape

### Top skill repos (February 2026)

| Repo | Stars | Skills | Our advantage |
|------|-------|--------|---------------|
| [obra/superpowers](https://github.com/obra/superpowers) | 56.6k | 20 | Meta-skills (process). Ours are domain-specific (iOS, accounting, CDN). |
| [anthropics/skills](https://github.com/anthropics/skills) | 72.7k | ~20 | Official Anthropic. Ours cover niches they don't (Norwegian market, Bunny.net). |
| [levnikolaevich/claude-code-skills](https://github.com/levnikolaevich/claude-code-skills) | — | 102 | Quantity-focused Agile skills. Ours are deeper per-skill (337 lines for Fiken). |

### Our differentiators

1. **Norwegian market skills** — fiken-accounting and klarsprak have zero competition
2. **Production-tested** — every gotcha comes from actual failures, not documentation reading
3. **Domain depth** — bunny-net covers an entire platform in one skill (CDN + storage + video + AI + edge + security)
4. **Novel techniques** — transparency-generation and parallel-research are unique methodologies

---

## Skills Inventory & Audit

### What's in the repo (12 skills)

| Skill | Lines | Shareability | Quality | Usefulness | Unique? |
|-------|-------|-------------|---------|------------|---------|
| parallel-research | 228 | 5/5 | 5/5 | 5/5 | Yes — 4-phase multi-agent methodology |
| ios-development | 787 | 5/5 (scrubbed) | 5/5 | 5/5 | Self-updating iOS 26 toolkit |
| fiken-accounting | 337 | 5/5 | 5/5 | 4/5 | Only Fiken skill in existence |
| bunny-net | 510 | 5/5 (scrubbed) | 5/5 | 5/5 | Full platform in one skill |
| instagram-pipeline | 184 | 4/5 | 5/5 | 3/5 | Requires `socmed` package |
| soft-glass-ui | 297 | 5/5 | 5/5 | 4/5 | iOS 26 glass design system |
| google-deep-research | 250 | 5/5 | 4/5 | 4/5 | Gemini Deep Research API |
| changelog | 165 | 5/5 (scrubbed) | 5/5 | 5/5 | Strategic memory with bootstrap |
| agent-browser | 350 | 5/5 | 5/5 | 5/5 | Complete command reference |
| klarsprak | 199 | 5/5 | 5/5 | 3/5 | Only Norwegian language skill |
| transparency-generation | 238 | 5/5 | 5/5 | 4/5 | Novel difference matting technique |
| company-research | 68 | 5/5 | 3/5 | 4/5 | Exa sub-agent architecture |

### What was NOT included (and why)

| Skill | Location | Reason excluded |
|-------|----------|----------------|
| Superpowers (19 skills) | `~/.claude/skills/superpowers/` | Third-party — belongs to [obra/superpowers](https://github.com/obra/superpowers) |
| checkin.no API | `vibelabs-emails/.claude/skills/checkin.md` | Customer IDs, phone numbers, person names |
| CRM | `vibelabs-emails/.claude/skills/crm.md` | 26 named partner companies, user email, absolute paths |
| dagskurs-workflow | `vibelabs-emails/.claude/skills/dagskurs-workflow.md` | WiFi password, promo codes, affiliate links, Supabase ID |
| course-email | `vibelabs-emails/.claude/skills/course-email.md` | Vibelabs-specific workflow |
| solar-extract | `~/.claude/skills/solar-extract/SKILL.md` | Norwegian solar business with org numbers |
| solar-research | `~/.claude/skills/solar-research/SKILL.md` | Same — solar lead generation |
| website-content | `vibelabs-monorepo/.claude/skills/website-content/SKILL.md` | Project-specific file paths |

### Privacy scrubbing performed

| Skill | What was removed |
|-------|-----------------|
| ios-development | Team ID `NDDZ5C4VGA` → `YOUR_TEAM_ID` (3 occurrences) |
| bunny-net | API key (8 occurrences), storage password (6 occurrences), zone IDs, CDN URLs → environment variables |
| changelog | Personal filesystem path → generic `/path/to/your/project` |

### Candidates for future addition

| Skill | What's needed | Value |
|-------|--------------|-------|
| bunny-net (rule) | Create passive gotcha reminders | Pairs with existing skill |
| website-content | Generalize file paths | Novel self-learning content pattern |
| course-email | Remove Vibelabs references | Date validation + safety workflow |
| checkin.no | Remove all customer data, make generic GraphQL event skill | Event management pattern |

---

## Reference: How skills.sh Works

- [skills.sh](https://skills.sh/) is the leaderboard for agent skills, created by Vercel (January 2026)
- No submission process — skills appear via `npx skills add` telemetry
- Ranking: All Time, Trending (24h), Hot
- Top skill has 282k installs (find-skills by Vercel)
- The `npx skills` CLI is the package manager:
  ```bash
  npx skills add simonstrumse/vibelabs-skills    # Install
  npx skills list                                  # List installed
  npx skills find <keyword>                        # Search
  npx skills check                                 # Check updates
  ```

## Reference: Plugin Distribution

Users install our repo via:
```bash
# Claude Code native
claude plugin add simonstrumse/vibelabs-skills

# npx skills (cross-agent)
npx skills add simonstrumse/vibelabs-skills

# Manual
git clone https://github.com/simonstrumse/vibelabs-skills.git
cp -r vibelabs-skills/skills/* ~/.claude/skills/
```

## Reference: Key Community Links

- [Agent Skills Specification](https://agentskills.io/specification)
- [Claude Code Plugins Docs](https://code.claude.com/docs/en/plugins)
- [Plugin Marketplaces Docs](https://code.claude.com/docs/en/plugin-marketplaces)
- [Anthropic Official Plugin Directory](https://github.com/anthropics/claude-plugins-official)
- [Anthropic Plugin Submission](https://clau.de/plugin-directory-submission)
- [skills.sh](https://skills.sh/)
- [skills.sh FAQ](https://skills.sh/docs/faq)
