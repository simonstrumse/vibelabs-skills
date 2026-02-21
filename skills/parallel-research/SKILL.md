---
name: parallel-research
description: Orchestrate hierarchical multi-agent research with lead agents, sub-agents, critical review, and synthesis. Use for deep investigation tasks, research projects, complex analysis, or when exploring unfamiliar topics thoroughly.
allowed-tools: Read, Grep, Glob, WebFetch, WebSearch, Task
---

# Parallel Research Orchestration

A systematic methodology for conducting thorough research using hierarchical multi-agent coordination.

## When to Use

- Deep investigation of complex topics
- Research requiring multiple perspectives
- Analysis with potential for conflicting viewpoints
- Exploratory work in unfamiliar domains
- Any research that benefits from devil's advocate review

## Agent Hierarchy

### 1. Lead Agent (Coordinator)
- Decomposes research question into threads
- Assigns threads to sub-agents
- Monitors progress and adjusts strategy
- Coordinates synthesis

### 2. Sub-Agents (Specialists)
- Execute specific research threads
- Focus deeply on assigned topic
- Report findings with confidence levels
- Identify gaps and uncertainties

### 3. Critical Review Agent (Devil's Advocate)
- Challenges findings from sub-agents
- Identifies weaknesses in reasoning
- Proposes alternative interpretations
- Stress-tests conclusions

### 4. Synthesis Agent (Integrator)
- Combines findings across threads
- Resolves conflicts between sources
- Produces coherent narrative
- Highlights remaining uncertainties

## Phase 1: Anticipatory Decomposition

**Lead Agent Responsibilities:**

1. **Analyze Research Question**
   - Identify core question and sub-questions
   - Map dependencies between research threads
   - Anticipate potential failures and alternative remedies

2. **Decompose Into Parallel Threads**
   - Create 3-6 independent research threads
   - Each thread: clear objective, search strategy, success criteria
   - Ensure threads cover different perspectives (not redundant)
   - Define explicit handoff protocols

3. **Launch Sub-Agents**
   ```
   Launch 4 research subagents in parallel:
   - Thread 1: [Specific focus and search strategy]
   - Thread 2: [Specific focus and search strategy]
   - Thread 3: [Specific focus and search strategy]
   - Thread 4: [Specific focus and search strategy]

   Each agent should return only:
   - Key findings
   - Evidence quality assessment
   - Confidence score
   - Conflicting information found
   ```

## Phase 2: Parallel Research Execution

**Research Sub-Agent Instructions:**

1. **Search Strategy**
   - Execute assigned searches
   - Use progressive disclosure (don't front-load context)
   - Cross-reference multiple sources for balance
   - Track evidence quality

2. **Validation Requirements**
   - Verify claims against original sources
   - Note contradictions or conflicts found
   - Assign confidence scores (high/medium/low)
   - Flag assumptions or gaps

3. **Return Format**
   ```
   Thread [N] Findings:

   KEY FINDINGS:
   - [Finding 1] (Confidence: high/medium/low)
   - [Finding 2] (Confidence: high/medium/low)

   EVIDENCE QUALITY:
   - [Source type, credibility, date]

   CONFLICTS DETECTED:
   - [Any contradictions between sources]

   GAPS/LIMITATIONS:
   - [What wasn't found or remains unclear]
   ```

## Phase 3: Critical Review (Devil's Advocate)

**Role Assignment:**
"You are a systematic skeptic. Your role is to identify risks, edge cases, failure modes, logical fallacies, and vulnerabilities. Focus on disagreement and counterarguments, not confirmation."

**Three-Fold Review:**

1. **Anticipatory Critique**
   - What could be wrong with these findings?
   - What alternative interpretations exist?
   - What evidence is missing or weak?

2. **Finding-by-Finding Challenge**
   - For each finding: "What if this is wrong?"
   - Identify logical fallacies or reasoning gaps
   - Check for confirmation bias

3. **Strategic Refinement**
   - What should agents have done differently?
   - Which low-confidence findings should be rejected?
   - What additional research is needed?

**Output Requirements:**
```
CRITICAL REVIEW REPORT:

STRONG FINDINGS (accept):
- [Findings that withstand scrutiny]

WEAK FINDINGS (reject or flag):
- [Findings with logical flaws, weak evidence]

IDENTIFIED RISKS:
- [Edge cases, failure modes]

CONFLICTS REQUIRING RESOLUTION:
- [Contradictions between threads]

ADDITIONAL RESEARCH NEEDED:
- [Gaps requiring follow-up]
```

## Phase 4: Conflict Resolution & Synthesis

**Conflict Resolution Framework:**

1. **Debate Pattern**
   - For each conflict, have research agents defend findings
   - Evaluate on: evidence quality, logical consistency, edge case coverage
   - Judge agent makes final decision

2. **Voting with Confidence Weighting**
   - Weight findings by confidence scores
   - Prioritize primary over secondary sources
   - Prioritize recent over outdated (when relevant)
   - Require minimum confidence threshold

3. **Cross-Referencing Validation**
   - Verify final synthesis against original sources
   - Ensure balanced perspective
   - Flag remaining uncertainties

**Synthesis Output Format:**
```
RESEARCH SYNTHESIS REPORT

EXECUTIVE SUMMARY:
[2-3 paragraph synthesis answering original question]

VALIDATED FINDINGS:
1. [Finding] (Confidence: X, Sources: Y)
2. [Finding] (Confidence: X, Sources: Y)

CONFLICTS RESOLVED:
- [How contradictions were resolved]

REMAINING UNCERTAINTIES:
- [What remains unclear]

RECOMMENDATIONS:
- [Actionable recommendations]

SOURCES:
- [Complete source list with hyperlinks]
```

## Quality Assurance Principles

1. **Parallel Validation**: Multiple verification paths catch different errors
2. **End-State Evaluation**: Focus on correct final synthesis, not process
3. **Separation of Generation and Evaluation**: Research and critique are separate
4. **Transparency**: Full disclosure of methodology and sources
5. **Human Escalation**: For irresolvable conflicts, escalate to user

## Claude Code Specific Optimizations

1. **Use Built-in Subagents**: Leverage Plan Subagent for orchestration, Explore Subagent for codebase research
2. **Parallel Execution**: Always execute independent threads in parallel
3. **Context Preservation**: Main thread maintains context; subagents use isolated windows
4. **Token Awareness**: This methodology uses significant tokens (4+ parallel agents)

## Common Pitfalls to Avoid

1. **Sequential Research**: Don't run threads one-by-one; parallelize
2. **Weak Devil's Advocate**: Enforce systematic skepticism
3. **Premature Synthesis**: Don't synthesize before critical review
4. **Context Bleeding**: Keep sub-agent contexts isolated
5. **Unchallenged Conflicts**: Require confidence thresholds and debate
6. **Missing Transparency**: Always include source attribution

## Success Metrics

A successful research orchestration produces:
- High-confidence findings (backed by multiple sources)
- Acknowledged and resolved conflicts
- Transparent limitations and uncertainties
- Actionable synthesis (not just information dump)
- Complete source attribution
- Evidence of critical review (not confirmation bias)
