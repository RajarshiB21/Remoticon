---
name: grilling
description: Interview the user about a plan, decision or idea when they explicitly request grilling or a stress-test conversation. Do not use as the default implementation workflow.
---

Interview the user relentlessly until you reach a shared understanding. Map this as a **design tree**: every decision branches into the decisions that hang off it.

Work the tree in **rounds**. The **frontier** is every decision whose prerequisites are already settled: the questions you can ask _now_ without guessing at answers you haven't heard yet. Ask **at most 5 questions per round**: number each question and give your recommended answer. If the frontier has more than 5 ready questions, ask the top 5 — the most foundational, most-blocking ones — this round, and carry the rest forward to the next round untouched. This caps the batch size only; it never shrinks the total number of questions the interview asks. Then wait for the user's answers before the next round.

Format a round like so:

```
1. **<question title>**: <question body, might be multiple paragraphs, including multiple choices>

Recommendation: <your recommended answer>

---

2. **<question title>**: <question body, might be multiple paragraphs, including multiple choices>

Recommendation: <your recommended answer>
```

Each round the user answers reshapes the tree: settled decisions push the frontier outward and unblock questions that depended on them. Recompute the frontier and ask the next round, again capped at 5: any questions carried over from last round compete with newly unblocked ones for the top 5 slots by how foundational/blocking they are. A question whose answer depends on another question still open in this round belongs to a _later_ round, not this one.

Finding _facts_ is your job, never the user's. Look up small local facts directly. Use the configured scout-master only for a bounded investigation within the task's authorized agent allocation; tool availability alone is not a reason to spawn an agent. Don't ask the user for anything you could look up yourself. Don't block on an unsettled lookup: only questions downstream of it wait for the result; ask the rest of the frontier now. The _decisions_ are the user's: put each to them and wait. An instruction to stop the conversation or proceed with an approved plan ends this interview mode.

Only the user ends the interview — never the model. Never end it abruptly, silently mark it complete, or assume a shared understanding has been reached on your own judgment. When the frontier empties — every branch of the design tree visited, nothing left silently assumed — that is not termination: say plainly that you've reached clarity and have no further questions, then stand by. Wait for the user to either raise new threads or explicitly call the interview over; only then may you act on it.
