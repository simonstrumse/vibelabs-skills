# Claude Code Meetup Follow-Up Email

For the Claude Code Meetup Oslo lightning talk on January 21, 2026.

## Subject Options

- The world-building harness I promised at the Claude Code meetup
- Build the world before you write the story
- The Claude Code world-building skill + prompt

## Short Version

```text
Hi all,

At the Claude Code Meetup Oslo on January 21, 2026, I promised I’d send over the world-building harness I use before building.

It’s now packaged as part of the Vibelabs skills repo as the `world-building` skill:
https://github.com/simonstrumse/vibelabs-skills

Install:
claude plugin add simonstrumse/vibelabs-skills

Then use:
Use $world-building to build the world before planning or coding [THING].

The idea is simple:
- map every pillar the project depends on
- run parallel research for each pillar
- synthesize what matters and what’s uncertain
- only then write the PRD, architecture, backlog, and build plan

If you want the shortest version, this 5-sentence prompt is the core of it:

I want you to build [THING] — but first, build the World: figure out every pillar this project depends on (users, market, competitors, business model, design language, tech constraints/integrations, legal/regulatory, ops, economics, risks) and write a quick pillar list and priorities.
Then run parallel research for each pillar and dump everything into folders (sources/links, raw notes, extracts, and what each source supports).
Then synthesize each pillar into "what matters, what's uncertain, decisions + assumptions, risks, and minimum facts needed to build correctly," and then do one cross-synthesis that resolves conflicts into a single strategy.
Only after that, write the PRD + UX flows + acceptance criteria + architecture plan + backlog, all strictly constrained by what's in the World.
Then build the product iteratively, and every time you learn something new, update the World first, then update the PRD/backlog, then update the code. Don't stop until the solution is finished and deployed.

/Simon
```

## Slightly More Polished Version

```text
Hi all,

At the Claude Code Meetup Oslo on January 21, 2026, I mentioned a method I use for getting hands-off but non-generic results out of Claude Code: build the world before you build the product.

I finally packaged it properly. It now lives in the Vibelabs skills repo as `world-building`:
https://github.com/simonstrumse/vibelabs-skills

Install it with:
claude plugin add simonstrumse/vibelabs-skills

Then prompt with something like:
Use $world-building to run deep research before building an AI workflow tool for freelance recruiters.

What the skill does:
- maps the pillars your project depends on
- creates a clean folder structure for research and synthesis
- runs parallel research threads
- forces cross-synthesis and critical review
- only then derives PRD, architecture, backlog, and build plan

It’s basically the operationalized version of the “World Building for AI” talk.

If useful, reply and I can also share the short 5-sentence version I use to kick this off in a fresh session.

Best,
Simon
```

## One-Line Social Version

```text
I finally packaged the “build the world before you write the story” harness from my Claude Code Meetup Oslo talk into a shareable Claude Code skill: https://github.com/simonstrumse/vibelabs-skills
```
