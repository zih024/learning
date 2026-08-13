# Learning

A set of claude skills for learning an entire field.

Methology: you give the AI the *whole landscape* of a subject at once, then use it to build a conceptual map, expose your blind spots, and pressure-test your understanding until it holds.

> **The philosophy:** Don't memorize information. Build connections.

---

## Step 0 — Feed the AI the whole field

Before asking anything, load in the full picture. Aim for something like:

- 5–10 textbooks or chapters
- 10–20 papers
- lecture slides
- transcripts
- notes

The idea is that the AI sees the **entire field** instead of one document at a time.

---

## The method

### 1. Don't ask for a summary

Summaries give you linear notes. Ask questions that build a *conceptual map* instead:

- "What are the first principles of this field?"
- "What are the core mental models?"
- "If I only remembered five ideas forever, what would they be?"
- "How do experts organize this knowledge?"

### 2. Build a dependency graph

Understand what depends on what:

- "What concepts must I understand before learning X?"
- "Draw the prerequisite tree."
- "What's the 20% that explains 80% of the field?"

### 3. Attack weaknesses

Rereading feels productive but isn't. Instead:

- quiz yourself
- explain concepts from memory
- ask the AI to find holes: *"Pretend you're an MIT professor. What misunderstandings would I likely have?"*

### 4. Generate analogies

Ask it to explain every difficult concept in multiple representations:

- mathematically
- intuitively
- visually
- with real-world examples
- by contrasting it with similar ideas

### 5. Socratic mode

Instead of asking for answers:

> "Don't tell me. Ask me questions until I discover it myself."

This forces active recall, which beats passive reading for retention.

### 6. Expert simulation

Once you think you understand:

- "Interview me like a PhD qualifying exam."
- "Debate me."
- "Give me edge cases."
- "Where would practitioners disagree?"

---

## What's in this repo

Three skills that implement the method, in the order you'd use them:

| Skill | What it does | Method steps |
|-------|--------------|--------------|
| **`domain-research`** | Ingests the whole field and produces a source-verified dossier plus a concept map and prerequisite graph. | 0, 1, 2 |
| **`concept-teacher`** | Teaches concept by concept in dependency order, using first principles and multiple representations. | 4, 5 |
| **`course-quizzer`** | Tests real understanding with recall, Socratic remediation, and expert-simulation debate. | 3, 6 |

Typical flow: **research a domain → learn it concept by concept → get quizzed until it sticks.**
