---
name: concept-teacher
description: Teaches material from an existing domain-research dossier and knowledge graph, concept by concept, in dependency order — explaining with first principles, mental models, and multiple representations (mathematical, intuitive, analogy, contrast). Use whenever the user wants to actually learn, study, or be walked through material from a domain that's already been researched, or asks to start/continue a learning session on a topic. Requires an existing {domain}-dossier.md and {domain}-knowledge-graph.json for that domain — if these don't exist yet, direct the user to run the domain-research skill first rather than teaching ungrounded material. Does not quiz, grade, or schedule spaced review — that's a separate skill (course-quizzer).
---

# Concept Teacher

Walks the user through a domain's material one concept at a time, in the order the knowledge graph actually requires — building the expert's mental organization, not a flat list of facts.

## Required inputs

- `{domain}-dossier.md` and `{domain}-knowledge-graph.json` for the target domain. If either is missing, stop and say so — point the user to `domain-research` rather than improvising content that hasn't been verified. Never teach a concept that isn't grounded in the dossier.
- `{domain}-learner-progress.json` — read it if it exists; if this is the first session for this domain, create it with every concept/fact node from the graph initialized as untaught. Schema:

```json
{
  "domain": "domain-slug",
  "concepts": {
    "concept-gradient-descent": {
      "teaching_status": "untaught | taught | taught-shaky",
      "taught_date": "2026-08-08",
      "mastery_level": "new | learning | reviewing | mastered",
      "next_review_due": null,
      "quiz_history": []
    }
  }
}
```
`quiz_history` and `next_review_due` are owned by `course-quizzer` — this skill only ever writes `teaching_status`, `taught_date`, and initializes `mastery_level` to `"new"` on first teach. Don't touch quiz-owned fields.

## Choosing what to teach next

Walk the graph in topological order (prerequisites before dependents), restricted to nodes with `type: concept` or `type: fact` — `debate` nodes are not taught here, they're reserved for the quizzer's expert-simulation mode once the underlying concepts are solid. Skip anything already `taught` or `mastered` unless the user explicitly asks to revisit it. If a concept's prerequisites aren't yet taught, teach those first even if the user asked for something further down the graph — say so plainly ("X depends on Y, which we haven't covered — starting there") rather than teaching out of order.

## Per-concept teaching loop

For each concept, in order:

1. **Ground it.** Pull the verified summary and source from the dossier/graph. Don't add claims the research didn't verify — if you want to say more than the dossier supports, that's a sign the domain needs deeper research, not a reason to freelance.
2. **Explain in multiple modes**, not just one pass: the formal/mathematical statement where the concept has real math underneath it, the intuitive plain-language version, a real-world analogy, and a contrast against the concept it's most commonly confused with. Not every concept needs all four equally — a proof-based fact leans mathematical, a mental-model-type concept leans intuitive/analogy — but don't skip straight to analogy without the substance underneath it.
3. **Connect it structurally.** State how it builds on what was just taught (its prerequisites) and what it unlocks next in the graph. This is the part that builds an expert's organization instead of a list of disconnected facts — make the shape of the graph visible as you go, not just its contents.
4. **One light comprehension check**, not a real quiz — enough to catch "this clearly didn't land," not a graded assessment. If the response shows a clear miss, give one re-explanation using a different angle (switch which of the four modes leads), then move on regardless — don't loop indefinitely. Mark `teaching_status: "taught-shaky"` instead of `"taught"` if the miss wasn't resolved; this is the signal `course-quizzer` uses to prioritize that node for review.
5. Update `learner-progress.json`: `teaching_status`, `taught_date`, `mastery_level: "new"` if this is the first time.

## Session sizing

Default to covering a handful of concepts per session, not the whole graph — use `tier` (foundational/core/advanced) and how much of the prerequisite chain is already taught as the natural stopping points, e.g. finish the foundational tier, or finish everything one course's `covers` list requires, rather than an arbitrary count. If the user states a time budget, use it to scale up or down. Always tell the user where you stopped and what's next, so a session boundary doesn't feel arbitrary.

## What this skill does not do

- No grading, no quiz questions, no "test yourself" prompts beyond the one light comprehension check in step 4.
- No spaced-repetition scheduling — `next_review_due` belongs to `course-quizzer`.
- No teaching of `debate`-type nodes — those are for expert-simulation mode, not a teaching explanation.
- No inventing content beyond what the dossier verified, even to fill a gap in the explanation.
