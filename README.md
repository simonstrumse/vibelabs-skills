# Vibelabs Skills

Production-tested agent skills built from real-world projects. Each skill encodes hard-won knowledge from building iOS apps, sending invoices, orchestrating research, managing CDN infrastructure, and automating browsers.

**13 skills** covering iOS development, Norwegian accounting, research orchestration, CDN/media management, audio transcription, browser automation, content pipelines, and more.

---

## Install

### Claude Code (plugin)

```bash
claude plugin add simonstrumse/vibelabs-skills
```

### npx skills (cross-agent)

```bash
npx skills add simonstrumse/vibelabs-skills
```

### Manual

```bash
git clone https://github.com/simonstrumse/vibelabs-skills.git
cp -r vibelabs-skills/skills/* ~/.claude/skills/
```

---

## Skills

### Development

| Skill | Description |
|-------|-------------|
| [**ios-development**](skills/ios-development/) | Comprehensive iOS toolkit: Liquid Glass design system, @Observable + SwiftData with VersionedSchema, MVVM architecture, App Store verification checklist. Self-updates by checking Apple Developer News. |
| [**soft-glass-ui**](skills/soft-glass-ui/) | iOS 26 Liquid Glass design system with native `.glassEffect()` APIs and gradient-based fallbacks for iOS 17-18. Design tokens, typography, color strategy, component patterns. |
| [**changelog**](skills/changelog/) | Strategic project memory system. Reads context before modifying code, documents changes and decisions while context is fresh. Bootstrap mode auto-populates from session history. |

### Research & Analysis

| Skill | Description |
|-------|-------------|
| [**parallel-research**](skills/parallel-research/) | Hierarchical multi-agent research: lead agents decompose questions, sub-agents execute threads, devil's advocate challenges findings, synthesis agent resolves conflicts. 4-phase methodology. |
| [**google-deep-research**](skills/google-deep-research/) | Google Gemini Deep Research API integration. Submits queries that run 5-20 minutes server-side, searching multiple web sources and producing 5,000-40,000 character reports. |
| [**company-research**](skills/company-research/) | Company intelligence using Exa search. Discovery, competitive analysis, LinkedIn profiles, news tracking. Token-isolated sub-agent architecture keeps context clean. |

### Infrastructure & APIs

| Skill | Description |
|-------|-------------|
| [**fiken-accounting**](skills/fiken-accounting/) | Norwegian Fiken accounting API. Invoices, credit notes, contacts, products, VAT handling. 11 critical gotchas documented with error messages. Multi-account support. |
| [**bunny-net**](skills/bunny-net/) | Bunny.net CDN platform: edge storage, pull zones, image optimization, AI image generation, video streaming, edge scripting, DNS, security (WAF/DDoS). Complete API reference. |
| [**instagram-pipeline**](skills/instagram-pipeline/) | Instagram saved posts pipeline: sync from API via Chrome cookies (~260 posts/min), parallel media download, Whisper audio transcription + OCR text extraction. **Self-contained** — bundled Python package in `scripts/`, one-command setup. |

### Content & Writing

| Skill | Description |
|-------|-------------|
| [**klarsprak**](skills/klarsprak/) | Norwegian plain language (klarsprak) guidelines from Sprakradet. The 7 principles, common mistakes, anglicisms to avoid, website copy patterns, quality checklist. |
| [**transparency-generation**](skills/transparency-generation/) | Generate AI images with true alpha transparency using difference matting. Mathematical foundation, validation pipeline, troubleshooting guide. Works with Vertex AI / Gemini. |

### Audio & Media

| Skill | Description |
|-------|-------------|
| [**transcribe**](skills/transcribe/) | VAD-first audio transcription pipeline: Silero-VAD segmentation, MLX Whisper (NB-Whisper for Norwegian, large-v3-turbo for English), dictionary correction, Claude LLM post-processing. Zero hallucinations, 9.6x realtime on M1. 8 critical gotchas documented. |

### Automation

| Skill | Description |
|-------|-------------|
| [**agent-browser**](skills/agent-browser/) | Complete browser automation reference: navigation, form filling, screenshots, video recording, cookies, network interception, semantic locators, proxy support, parallel sessions. |

---

## What Makes These Different

These aren't toy examples. Each skill was built to solve real problems in production:

- **ios-development** encodes months of iOS 26 Liquid Glass patterns, deprecated API tracking, and App Store rejection prevention
- **fiken-accounting** documents 11 API gotchas discovered through actual invoice failures (wrong VAT codes, missing bank accounts, silent field ignoring)
- **parallel-research** is the methodology behind 100+ research projects spanning competitive analysis, market research, and technical deep-dives
- **bunny-net** covers the full Bunny.net platform from a single skill (CDN, storage, video, AI, edge compute, security)
- **instagram-pipeline** processes 12,000+ saved posts with Chrome cookie auth, parallel downloads, and Whisper+OCR extraction
- **transcribe** is the result of evidence-backed research (Baranski et al., 2025) and benchmarking against 4 alternative approaches — zero hallucinations where competitors had 7-44x repeated phrases

---

## Compatibility

Built for the [Agent Skills specification](https://agentskills.io/specification). Works with:

- Claude Code
- GitHub Copilot (Codex CLI)
- Cursor
- Gemini CLI
- Any agent supporting the Agent Skills spec

---

## Skill Structure

Each skill follows the standard format:

```
skills/
  skill-name/
    SKILL.md       # YAML frontmatter + markdown instructions
    scripts/       # Optional: bundled executable code + dependencies
```

The YAML frontmatter includes:
- `name` — Skill identifier
- `description` — What it does + when to trigger
- `allowed-tools` — Pre-approved tools (where applicable)
- `license` — MIT

Skills with `scripts/` directories (like `instagram-pipeline`) are self-contained — they include all executable code and a setup script for one-command installation.

---

## Contributing

Found a bug or have an improvement? PRs welcome.

**Guidelines:**
- Keep skills self-contained (no external dependencies unless documented)
- Follow the [Agent Skills specification](https://agentskills.io/specification)
- Include practical examples, not just API docs
- Document gotchas and edge cases from real usage

---

## License

MIT - see [LICENSE](LICENSE)

---

Built by [Vibelabs](https://vibelabs.no) in Oslo, Norway.
