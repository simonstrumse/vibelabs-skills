# Five-Sentence Superprompt

This is the shortest public version of the harness from the January 21, 2026 Claude Code Meetup Oslo talk.

```text
I want you to build [THING] — but first, build the World: figure out every pillar this project depends on (users, market, competitors, business model, design language, tech constraints/integrations, legal/regulatory, ops, economics, risks) and write a quick pillar list and priorities.
Then run parallel research for each pillar and dump everything into folders (sources/links, raw notes, extracts, and what each source supports).
Then synthesize each pillar into "what matters, what's uncertain, decisions + assumptions, risks, and minimum facts needed to build correctly," and then do one cross-synthesis that resolves conflicts into a single strategy.
Only after that, write the PRD + UX flows + acceptance criteria + architecture plan + backlog, all strictly constrained by what's in the World.
Then build the product iteratively, and every time you learn something new, update the World first, then update the PRD/backlog, then update the code. Don't stop until the solution is finished and deployed.
```

Use this when:
- the user wants the lightweight version
- you need a shareable public snippet
- the session should start from one compact instruction instead of a long harness
