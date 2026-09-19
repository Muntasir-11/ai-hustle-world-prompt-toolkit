# The Context Budget Test

A 3-question check for deciding what actually belongs in a model's context window — for a single prompt or a long-running agent.

Full guide with the research behind this: [Context Engineering Explained: How to Give AI Models the Right Information](https://aihustleworld.com/2026/09/context-engineering-explained.html)

## Why this exists

Attention is a finite, competed-for resource, not an unlimited spotlight. Research testing 18 frontier models found every one of them degrading in accuracy as input length grew — even when every added token was genuinely relevant and nowhere near the model's actual context limit ("context rot"). A bigger context window doesn't fix this; curation does.

## The three checks

Run these against every piece of context before it goes in — a document, a tool definition, a chunk of conversation history, anything.

### 1. Necessity Check
**Does the model need this right now, or could it be fetched just-in-time only if the task actually requires it?**

If this piece of context turned out to be unnecessary, would removing it have changed anything about how the model needed to behave? If no — it's a candidate for just-in-time retrieval instead of upfront inclusion. Unused context still costs attention budget even when the model never needed it (this applies to tool definitions too — a tool the model never calls still costs budget just by being defined).

### 2. Position Check
**If something is genuinely critical, is it placed where the model will actually attend to it — near the start or end — rather than buried in the middle?**

Models reliably use information near the start or end of a long context and lose track of the same information buried in the middle ("lost in the middle"). For anything a system genuinely cannot afford to miss — a hard safety constraint, a non-negotiable business rule — repeat it near the end of the context, immediately before the model generates its response.

###3. Decay Check
**As a task continues across many turns, does this piece of context still deserve its spot, or has it become stale, already-used, or safely summarizable?**

At regular intervals in a long session, ask: would this still be included if you were starting the task fresh right now, knowing everything you know at this point? Anything that fails is a compaction or removal candidate, not a permanent fixture.

## Quick-reference table

| Check | Question | If it fails |
|---|---|---|
| Necessity | Does the model need this now, or only if the task requires it? | Wasted attention budget on information that never gets used |
| Position | Is critical information placed where the model actually attends? | The exact "lost in the middle" failure |
| Decay | Does this still deserve its spot as the task continues? | Context rot compounds the longer a session runs |

## The five components of context (what you're actually curating)

1. **System instructions** — the behavioral rules for the whole task (see the [System Instruction Blueprint](system-instruction-blueprint.md))
2. **The immediate request** — the specific thing being asked right now
3. **Retrieved/grounding data** — documents, database records, search results (typically via RAG)
4. **Tool and function definitions** — what actions the model can take
5. **Conversation history and memory** — what's already happened in the session

All five compete for the same finite attention budget — adding more of one doesn't just cost tokens, it reduces the effective attention share available to the other four.

## A simple rule of thumb

If you could screenshot the entire relevant context for a task in one image, you probably don't have a context engineering problem yet. This discipline earns its keep once a task's relevant information stops fitting comfortably in view all at once — multi-step agents, tool-calling systems, long-running conversations.

## What to watch for

- A long-running agent's tool choices start to drift, or it stops following an instruction it was clearly given earlier — usually a **decay** problem, not a capability problem.
- Task success rate dropping specifically in *long* sessions, not short clean test cases — the whole point of context rot is that failures concentrate later in a session.
- A system prompt or fact that worked in the first few exchanges and quietly stops being followed later — the instruction losing its share of a shrinking attention budget, not the model "forgetting" it.
