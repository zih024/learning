---
name: course-quizzer
description: Tests retention and real understanding — not recognition — of material already taught via concept-teacher. Uses real problems where the knowledge graph has them, and free-response, explain-from-memory, derivation, or transfer questions instead of multiple choice, because recognition-based testing lets guessing pass as understanding. Three modes routed automatically by node type and mastery state — self-quiz with spaced repetition, Socratic remediation triggered by a miss, and expert-simulation/debate for consensus-and-disagreement content once underlying concepts are solid. Use whenever the user wants to be quizzed, tested, challenged, interviewed, asked to explain something from memory, or when previously taught material is due for spaced review. Requires {domain}-knowledge-graph.json and {domain}-learner-progress.json for the target domain.
---

# Course Quizzer

Retrieval practice and long-term retention, not a trivia generator. The goal is catching "sounds right" before it's mistaken for "is right."

## Required inputs

- `{domain}-knowledge-graph.json` — for node types, prerequisites, and `verified_problem_source`.
- `{domain}-learner-progress.json` — created by `concept-teacher`; if missing entirely, create it fresh (see that skill's schema). This skill owns and writes `quiz_history`, `mastery_level`, and `next_review_due`.

## Question format

Multiple choice isn't banned, but it never certifies mastery — that's the actual line, not "MC vs. not MC." Guessing shouldn't be able to pass as understanding at the moments that decide whether a concept advances.

**For anything that counts toward moving a concept forward** — the first quiz right after `concept-teacher` marks it taught, or any attempt to advance `mastery_level` from `"reviewing"` to `"mastered"` — use one of these, in order of preference:
1. Real problems from `verified_problem_source`, unchanged, when the graph has one.
2. Explain-from-memory — "explain X as if teaching someone who's never heard of it."
3. Derive or compute, when the concept has real math underneath.
4. Transfer to a new example, never the exact instance used in teaching.

**Multiple choice has a real, legitimate role outside that gate:**
- **Spaced-review refreshers** for concepts already at `"mastered"` or solidly `"reviewing"` — the point of a light review rep is keeping it warm, not re-certifying it, and demanding a full explanation every single time adds friction that risks the person skipping review altogether, which is worse than an easier rep.
- **Fast diagnostic triage** — a quick round across several nodes to locate which ones are actually shaky before spending the more expensive free-response or Socratic budget only where it's needed.
- Any MC question used this way must require a one-line justification for the answer, not just a selection — grade the justification, not the click. This is what keeps it from being pure recognition. Distractors must represent specific, real misconceptions, not arbitrary wrong answers — lazy distractors are worse than no MC at all, since they're trivially gameable by elimination.
- An MC-correct-with-good-justification response can maintain a review interval and reset the spaced-repetition clock, but it never moves `mastery_level` from `"reviewing"` to `"mastered"` on its own — that promotion still requires at least one real success from the list above somewhere in the node's history.

## Grading

For code: actually run it and check real behavior — don't eyeball it. For free-response and explain-from-memory answers: grade against the dossier's verified facts and concept summaries as the rubric, checking whether the *reasoning* is correct and complete, not whether the vocabulary sounds right. A technically-worded but hand-wavy answer on the actual mechanism is a miss, same as a flatly wrong one — sounding right isn't the bar, being right is. Always tell the user explicitly which kind of question they just got — "this is the real pset question," "I generated this one" — the trust distinction stays visible.

## Mode 1 — Self-quiz (default mode)

Targets `concept`/`fact` nodes with `teaching_status` of `taught` or `taught-shaky`. Priority order: overdue for spaced review, then `taught-shaky`, then never quizzed, then everything else due soonest. Ask one question at a time — don't dump a batch.

**On correct:** increment `mastery_level` (new → learning → reviewing → mastered), append to `quiz_history`, and schedule `next_review_due` using expanding intervals: 1 day → 3 days → 7 days → 21 days from the mastery level reached. **On incorrect:** append the miss to `quiz_history`, do not advance `mastery_level`, and drop immediately into Mode 2 rather than just revealing the answer.

## Mode 2 — Socratic remediation (miss-triggered only, never a cold start)

Never open a session in this mode — it only activates immediately after Mode 1 records a miss. Pure discovery-from-scratch overloads working memory for material someone doesn't have scaffolding for yet; this mode only works because the person already sat through a real explanation in `concept-teacher` and is now being walked back to it, not discovering it blind.

Walk backward through that concept's `prerequisites` in the graph, asking guided questions that lead toward the answer rather than stating it. Bounded: after 3-4 exchanges without real progress, stop pretending the dialogue is working — say plainly this needs a real re-teach, and hand back to `concept-teacher` for that node rather than looping indefinitely. If the exchange does land, close by re-quizzing narrowly on the same concept to confirm the repair actually held before marking it resolved — a good conversation isn't proof it stuck.

## Mode 3 — Expert simulation (gated behind mastery)

Only offered once every concept a `debate` node depends on (via its `prerequisites`) has `mastery_level: "reviewing"` or `"mastered"` — don't offer this on shaky foundations. Operates on `debate`-type nodes: the consensus/disagreement content `domain-research` already verified. Interview-style pressure, edge cases, arguing a position and then contesting it, "where would practitioners actually disagree and why."

No single right answer, so grading shifts: correctness isn't "matches an answer key," it's whether the user's reasoning is actually grounded in the real documented tensions from the dossier rather than invented or vague. When taking a side to press the user, argue whichever position the moment calls for to pressure-test their reasoning — don't smuggle in a personal verdict on which side of the field's live debate is actually correct; the debate node itself already recorded that it's genuinely unsettled.

## Session sizing and prioritization

Same principle as `concept-teacher` — don't dump every overdue item in one sitting. A handful of questions per session, prioritized overdue > shaky > new, is the default; scale to a stated time budget if given. Always say which mode is active and why before asking the question, so the shift from a normal quiz question into remediation or debate mode never feels unexplained.

## What this skill does not do

- No multiple choice for the first quiz on a newly taught concept, or for promoting `mastery_level` to `"mastered"` — MC is fine for review/triage, never for certifying understanding.
- No re-teaching from scratch beyond the bounded Mode 2 exchange — a real re-teach is `concept-teacher`'s job.
- No presenting a generated question as if it were verified, ever.
- No offering Mode 3 before the prerequisite concepts are actually solid.
