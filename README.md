# Vibelabs Skills Marketplace

Production-tested Claude Code plugins built from real-world Vibelabs projects.

This repo is now a **marketplace** containing **4 installable plugins** built from **14 underlying skills**.

---

## Install

### Claude Code marketplace

```bash
/plugin marketplace add simonstrumse/vibelabs-skills
```

Then install whichever plugin you want:

```bash
/plugin install vibelabs-research@vibelabs
/plugin install vibelabs-development@vibelabs
/plugin install vibelabs-operations@vibelabs
/plugin install vibelabs-content@vibelabs
```

### Manual / raw skills

The raw skills still live in [`skills/`](skills/) and can be copied directly:

```bash
git clone https://github.com/simonstrumse/vibelabs-skills.git
cp -r vibelabs-skills/skills/* ~/.claude/skills/
```

---

## Plugins

| Plugin | What it includes |
|-------|-------------------|
| [**vibelabs-research**](plugins/vibelabs-research/) | `world-building`, `parallel-research`, `google-deep-research`, `company-research` |
| [**vibelabs-development**](plugins/vibelabs-development/) | `ios-development`, `soft-glass-ui`, `changelog` |
| [**vibelabs-operations**](plugins/vibelabs-operations/) | `bunny-net`, `fiken-accounting`, `agent-browser` |
| [**vibelabs-content**](plugins/vibelabs-content/) | `transcribe`, `instagram-pipeline`, `transparency-generation`, `klarsprak` |

---

## Quick Picks

- Want the meetup method? Install `vibelabs-research@vibelabs` and use `$world-building`.
- Want the iOS stack? Install `vibelabs-development@vibelabs`.
- Want infra + ops tools? Install `vibelabs-operations@vibelabs`.
- Want media and content pipelines? Install `vibelabs-content@vibelabs`.

---

## Why This Structure

Many skills in one repo do **not** automatically make a marketplace. A marketplace distributes **plugins**. This repo now does both things cleanly:

- `skills/` is the source-of-truth for the raw skills
- `plugins/` groups those skills into focused installable bundles
- `.claude-plugin/marketplace.json` turns the repo into a Claude Code marketplace

The plugin directories use symlinks back to the shared `skills/` folders so the skill content only lives in one place.

---

## Skill Inventory

### Research

- [**world-building**](skills/world-building/) — Build the world before you write the story. Advanced context engineering harness: pillar map, folder scaffolding, parallel research, layered synthesis, critical review, and evidence-linked PRD/build plan.
- [**parallel-research**](skills/parallel-research/) — Hierarchical multi-agent research: lead agents decompose questions, sub-agents execute threads, devil's advocate challenges findings, synthesis agent resolves conflicts.
- [**google-deep-research**](skills/google-deep-research/) — Google Gemini Deep Research API integration.
- [**company-research**](skills/company-research/) — Company intelligence using Exa search.

### Development

- [**ios-development**](skills/ios-development/) — Comprehensive iOS toolkit with Liquid Glass, SwiftData patterns, and App Store verification guidance.
- [**soft-glass-ui**](skills/soft-glass-ui/) — iOS 26 Liquid Glass design system.
- [**changelog**](skills/changelog/) — Strategic project memory system.

### Operations

- [**fiken-accounting**](skills/fiken-accounting/) — Norwegian Fiken accounting API workflows.
- [**bunny-net**](skills/bunny-net/) — Bunny.net CDN, storage, video, AI, and security tooling.
- [**agent-browser**](skills/agent-browser/) — Browser automation reference.

### Content & Media

- [**transcribe**](skills/transcribe/) — VAD-first audio transcription pipeline.
- [**instagram-pipeline**](skills/instagram-pipeline/) — Instagram saved-posts pipeline with transcription and OCR.
- [**transparency-generation**](skills/transparency-generation/) — True-alpha image generation workflow.
- [**klarsprak**](skills/klarsprak/) — Norwegian plain-language guidance.

---

## Repo Structure

```text
.claude-plugin/marketplace.json   # Marketplace manifest
plugins/                          # Installable plugin bundles
skills/                           # Shared source-of-truth skill folders
docs/                             # Launch, marketing, and email docs
```

---

## License

MIT - see [LICENSE](LICENSE)

---

Built by [Vibelabs](https://vibelabs.no) in Oslo, Norway.
