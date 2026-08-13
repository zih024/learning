---
name: domain-research
description: Produces a deep, source-verified research dossier on a single target domain the user specifies — top-tier university course roadmaps, canonical textbooks, the landscape of leading researchers, where they agree/disagree and why, and a separate layer of settled scientific facts. Research-only — does NOT generate quizzes, study plans, or pacing. Runs on one domain at a time; wait for the user to name the domain before starting, don't assume or default to one. Use whenever the user wants to deeply understand a field before building a course, asks to "research" a domain, wants to know what top schools teach and in what order, wants to know who the leading figures are and what they actually agree/disagree on, or wants authoritative, non-random, high-quality sources on a technical subject. Always trigger this before curating a course for a new domain.
---

# Domain Research

Builds a verified, high-signal research dossier on the domain the user specifies. One domain per run — if no domain has been named yet, ask which one before doing anything else, rather than guessing or defaulting to an example. The output is reference material a person can trust — not a survey of whatever ranks well in search results.

## Step 0: Verification Protocol — run on every candidate source before it's used for anything

This is a gate, not a guideline. **Topical relevance is not sufficient for inclusion.** A source can be accurate, well-written, and directly on-topic and still fail this bar — if it fails, it does not go in the dossier, full stop. This skill exists specifically to prevent the failure mode of confidently citing a random blog post, an SEO "top 10" roundup, a self-published book, or a fringe/controversial author just because it showed up in search results and said something relevant.

Before any course, textbook, researcher claim, or fact enters the dossier, run it through this checklist. Do this explicitly, not as a vibe check:

1. **Origin check.** Is this from the institution's own domain — an official course page, department catalog, or professor-hosted syllabus on a .edu (or equivalent primary) domain? Or, for research claims, a peer-reviewed paper or the researcher's own stated words (talk, interview, paper)? A third-party site *describing* a course (a blog's "top MIT courses" roundup, a course-aggregator site) does not pass, even if the description is accurate — go find the institution's own page instead.
2. **Textbook chain-of-custody.** A textbook only counts as canonical if you can point to the specific syllabus URL that assigns it as primary or recommended reading. "This is considered a standard text in the field" from a review site or forum is not verification — find the syllabus or don't include the book.
3. **Author/source legitimacy.** Is the author or institution mainstream and credentialed within the field — not self-published, not a controversial or fringe figure, not someone whose authority rests on popularity rather than institutional or peer standing? If there's real doubt, exclude rather than include with a caveat.
4. **Liveness.** Did you actually open and check the URL in this session? A remembered fact about a course from training data does not pass — memory is not verification, only a live check is.
5. **Pass/fail, not soft-pass.** Each source gets a binary outcome. If it fails any check above, it is rejected outright — do not downgrade it to "worth mentioning" or fold it in with a hedge. If you're genuinely unsure whether something clears the bar, exclude it from the dossier and note in the output that it was considered and rejected, rather than including it with a soft qualifier.

**Institutions.** Default source pool is MIT, UC Berkeley, Stanford, Princeton, CMU, UPenn, and Harvard. If none of these seven have strong material in a subdomain, it's fine to pull from another top-tier program (Caltech, Georgia Tech, etc.) — but flag explicitly in the output when this exception is used and why.

**Transparency requirement.** Every dossier includes a short "Sources rejected" note listing anything that came up during research but failed the checklist and why — this makes the filtering auditable instead of invisible, and lets the user see the bar is actually being enforced, not just claimed.

**Every factual claim about a course or textbook needs a live source URL in the final output.** Do not state a course exists, a professor teaches it, or a textbook is assigned without having actually found and checked the page in this session.

## What to produce, per domain

Structure the dossier in five parts. Always include all five — skipping the "disagreement" or "facts" section defeats the purpose of this skill.

### 1. Course roadmap
For the domain, find the actual course sequences at the target schools (prerequisites → the course itself → what it unlocks next). Cross-reference at least 2-3 schools per domain where possible, since a single school's structure could be idiosyncratic; where schools structure the sequence differently, note the divergence rather than picking one arbitrarily.

Course-level sequencing is the outer structure, but it's too coarse to build testable checkpoints from later. Within each course, break out the individual concepts it teaches as discrete, explicitly-IDed units with their own prerequisite links back to earlier concepts (e.g. "backpropagation" depends on "gradient descent" and "the chain rule," not just "belongs to CS230"). This concept-level graph is what the roadmap section is really for — the course names are just provenance/grouping on top of it. Give every concept a short stable id (e.g. `concept-gradient-descent`) so later steps in the workflow can reference it without re-deriving it from prose.

### 2. Canonical texts
The textbooks that are actually assigned (verified per the rule above), organized against the roadmap from part 1 — which text maps to which stage. Note edition, since some fields (ML especially) move fast enough that older editions are stale on specific chapters even if the fundamentals hold.

### 3. Researcher landscape
Identify the leading living practitioners and researchers actually shaping the field right now — not a historical who's-who, the current landscape (analogous to Hassabis, Karpathy, Jeff Dean, Fei-Fei Li for AI — find the equivalent tier of figures for whichever domain you're researching). Search to confirm their current role/affiliation rather than relying on memory, since this changes. For each, note their specific area of focus within the domain — this matters for part 4.

### 4. Consensus and disagreement — synthesized, not reported
This is the section that separates this skill from a generic research summary. Do not produce a list of "Person A says X, Person B says Y." Instead:
- Identify the 3-6 major open questions or directions in the field right now.
- For each, state what the leading figures broadly converge on (grounded in things they've actually said publicly — talks, papers, interviews — not assumed).
- For each, state where they genuinely diverge, and reason about *why* — differing empirical bets, differing time horizons, differing institutional incentives, differing definitions of the problem. The "why" is the valuable part; a list of disagreements without structural explanation is not useful.
- Where a question is genuinely unsettled in the field (not just among a couple of individuals), say so plainly rather than manufacturing false consensus or false controversy.
- Treat this as a fair, evenhanded map of the field's live debates, not a place to inject a personal verdict on who's right.
- Tag each debate with which concept id(s) from section 1 a person needs to already understand before the debate makes sense (e.g. you can't meaningfully engage the scaling-laws debate without the `concept-scaling-laws` node) — this is what lets a later step place the debate at the right point in a sequence instead of introducing it too early.

### 5. Established facts (separate from opinion)
A distinct section listing what's actually settled — mathematically proven, experimentally verified, or definitionally true — as opposed to current research direction or opinion. This is the stable ground-truth layer that doesn't shift with the field's fashion. Be precise about what tier of certainty something has (e.g. a proven theorem vs. a strong empirical trend vs. an engineering rule of thumb people call a "law" loosely). Don't blend this section with part 4 — a fact isn't a "consensus position," it's just true. Where a fact directly corresponds to a concept node from section 1, reference that concept's id rather than restating it as an unconnected item.

## What this skill does NOT do

- No quizzes, practice problems, or self-testing material — that's a separate skill.
- No pacing, weekly schedules, or time estimates — that's curation, not research.
- No generated/invented courses or textbooks "in the spirit of" what a school might teach — only real, verified ones.

## Step 0.5: Check for other domain knowledge graphs before building new nodes

Before creating any concept node, check whether other `{domain}-knowledge-graph.json` files are available (e.g. uploaded to a Claude Project's knowledge base, or otherwise present in context). If they are:
- Search them first for any concept the new domain also depends on. If it already exists in another domain's graph, **reference it instead of redefining it** — use a domain-prefixed id in `prerequisites` or `covers`, e.g. `"ml-training:concept-gradient-descent"` instead of creating a duplicate `concept-gradient-descent` in the new file.
- Only create a new node when the concept genuinely doesn't exist yet in any available graph.
- This avoids re-verifying and re-describing the same shared trunk concepts every time a new branch domain is researched, and keeps one source of truth per concept instead of several slowly-drifting copies.

If no other domain graph files are available in this session (standalone run, no Project, first domain ever researched), fall back to fully self-contained node definitions as normal — don't block or ask the user to go set up a Project first. Note in the dossier's "Sources rejected" section (or a brief note near it) whether cross-referencing was used or this was a self-contained run, so it's clear which mode produced the file.

## Output format

Produce **two artifacts per run, generated together from the same research so they can't drift apart**:

**1. The dossier (`{domain-slug}-dossier.md`)** — the five-part Markdown document described above, for a human to actually read. Inline source links for every claim, plus the "Sources rejected" note.

**2. The knowledge graph (`{domain-slug}-knowledge-graph.json`)** — the machine-readable handoff to the next two skills in the workflow (dependency-graph/mental-model mapping, and eventually quizzing). This is not optional or a nice-to-have — without it, later skills have to re-parse prose to reconstruct structure that already existed at write-time, which loses information and introduces drift. Minimal schema, one node per concept/fact/debate/course identified in the dossier:

```json
{
  "domain": "domain-slug",
  "nodes": [
    {
      "id": "concept-gradient-descent",
      "type": "concept | course | fact | debate",
      "title": "Gradient Descent",
      "tier": "foundational | core | advanced",
      "prerequisites": ["concept-linear-algebra", "concept-partial-derivatives"],
      "summary": "One or two sentence description, not the full dossier prose.",
      "source_url": "https://...",
      "verified_problem_source": "https://... (a real pset/exam covering this, if the verification step found one; omit if none exists — do not invent one)"
    },
    {
      "id": "course-example-101",
      "type": "course",
      "title": "Example University 101",
      "tier": "foundational",
      "prerequisites": ["course-example-050"],
      "covers": ["concept-gradient-descent", "concept-loss-functions"],
      "summary": "...",
      "source_url": "https://..."
    }
  ]
}
```

`prerequisites` on a course node is course-to-course sequencing (what to take first). `covers` on a course node is course-to-concept: which concept ids that course actually teaches — without this field, the course graph and the concept graph are disconnected and a later step can't answer "which course teaches concept X," which defeats the point of tracking courses at all. Ids are unprefixed for nodes within this same file (`concept-gradient-descent`) and domain-prefixed for nodes borrowed from another domain's graph per Step 0.5 (`ml-training:concept-gradient-descent`) — a later step needs to know whether to look in this file or another one. Every node's `prerequisites` and `covers` lists must only reference `id`s that actually exist (in this file, or, for prefixed ids, in the referenced domain's file) — no dangling references. It's fine and expected for foundational math concepts (linear algebra, basic probability) to have zero covering courses in the domain's own course list, since those are assumed incoming background, not something the domain's courses teach — don't force a fake covering course just to fill the field. `verified_problem_source` should only be filled in when Step 0 actually verified a real problem set or exam question covering that node; leave it out rather than guessing, since a later quizzing step will trust this field as "a real checkable question exists here."
