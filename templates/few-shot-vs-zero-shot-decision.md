# Few-Shot vs. Zero-Shot: A Decision Guide

"Give it examples" is not a universal upgrade. Zero-shot and few-shot solve different problems — picking the wrong one wastes tokens at best and degrades output at worst.

Full guide with the research behind this: [Few-Shot vs Zero-Shot Prompting: When Should You Use Each?](https://aihustleworld.com/2026/09/few-shot-vs-zero-shot-prompting.html)

## The core distinction

- **Zero-shot** — describe the task directly, no worked examples. Right default for common, well-understood tasks (summarization, general Q&A, translation, most everyday writing) that closely resemble what a model was instruction-tuned to do.
- **Few-shot** — include 1–5 worked input/output examples before the real task. Earns its keep when a task has a specific, non-obvious format a plain instruction struggles to convey precisely.

## Decision table

| Situation | Recommended approach |
|---|---|
| Common task (summarization, general Q&A, translation) | Zero-shot |
| Task requires visible step-by-step reasoning | Zero-shot + "think step by step," especially on reasoning models |
| Output must match an exact, unusual format or schema | Few-shot, 2–3 diverse examples |
| Task has ambiguous categories specific to your business | Few-shot, examples covering each category and edge case |
| Using a reasoning model (o-series, extended thinking) | Zero-shot by default; reserve examples for formatting only |
| Task is high-volume, cost-sensitive, and zero-shot already works | Zero-shot — don't add unnecessary examples |
| Task has failed with few-shot and you have scale/budget | Fine-tuning or many-shot in-context learning |

## The reasoning-model wrinkle

Reasoning-focused models (OpenAI's o-series, Claude's extended thinking) generate their own internal chain-of-thought by default. Rigid worked examples — especially ones showing a specific reasoning path — can **suppress** that process rather than help it, nudging the model toward mimicking your example's steps even when a different path would suit the new problem better. On these models: clear, direct instructions first; reserve examples for strict output-formatting needs, not for teaching the task itself.

## When few-shot backfires: the overfitting trap

Models don't just learn the task from your examples — they pick up *incidental* patterns you never intended to teach:

- If every example produces a 2-sentence answer, the model may rigidly produce 2-sentence answers even when a case calls for one sentence or five
- If every example shares a word choice or sentence structure, that stylistic tic bleeds into every output
- Creative/brainstorming tasks tend to perform **worse** with few-shot — demonstrations anchor creativity to your examples' specific style, narrowing variety instead of raising the quality bar

**Fix:** treat your example set like a small training set — vary length, vary structure, keep constant only what should actually be constant.

## How many examples, and which ones

- No universal number. **One** well-chosen example is often enough to fix a formatting issue; **3–5** diverse examples suit tasks with real variation.
- **Diversity matters more than count** — near-duplicate examples teach less than examples each illustrating a distinct edge case.
- **Order matters** — models can weight the most recent example more heavily. Place your strongest, most representative example last.

## Where few-shot clearly wins regardless of model

**Tool-calling / agentic function calls.** Tool-call formats are rigid and machine-parsed — a model that's 95% correct on a JSON tool call is, for practical purposes, wrong, since most parsers reject a malformed argument outright. Show 2–3 examples of correctly formatted calls, including one edge case (an optional parameter omitted, a single-item list argument).

## Cost math worth knowing

A 5-shot prompt where each example runs 150 tokens adds 750 tokens of input **on every single request**. At 100,000 requests/month, that's 75 million extra input tokens purely for demonstrations. Test a zero-shot baseline first — if it's already good enough, few-shot is pure added cost with no offsetting benefit.

**Prompt caching softens this** when your examples are static and sit at the front of the prompt — most providers discount cached prefixes. Structure prompts as: static content (including examples) first, then variable per-request content last, to actually get the caching benefit.

## Two minimal templates

**Zero-shot baseline:**
```
[Role/context, briefly]
[Task as a direct instruction]
[Exact output format wanted]
[If multi-step logic: "Think through this step by step before answering."]
```

**Few-shot (once testing shows zero-shot isn't enough):**
```
[Same role/task instruction]

Input: [example 1 input]
Output: [example 1 output]

Input: [example 2 input, a genuine edge case]
Output: [example 2 output]

Input: [the real input you want handled]
Output:
```

## How to actually decide (don't guess)

1. Build a small evaluation set — 20–50 representative cases covering real variation, including the trickiest edge cases you can find.
2. Run both a zero-shot and few-shot version against that same set.
3. Score both on accuracy against a correct-answer key, plus token cost and latency.
4. Revisit after switching underlying models — a few-shot prompt tuned for one model generation can perform worse on a newer one.
