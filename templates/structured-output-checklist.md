# Structured Output / Schema-Writing Checklist

A reusable checklist and reliability framework for getting consistent JSON out of an LLM — for pipelines, not just chat.

Full guide with the research behind this: [How to Use Structured Prompts for Reliable JSON and Data Extraction](https://aihustleworld.com/2026/09/structured-prompts.html)

## The reliability ladder

Structured output isn't one setting — it's four layers, each adding a stronger guarantee at a small cost in setup, latency, or provider lock-in.

| Layer | What it is | Guarantees |
|---|---|---|
| 1. Prompt-level technique | Clear schema description, field definitions, 1–2 worked examples | Nothing enforced — relies on the model following instructions |
| 2. JSON mode | Provider setting constraining output to valid JSON syntax | Valid JSON syntax — **not** any particular schema, fields, or types |
| 3. Native structured outputs | OpenAI strict schema mode, Anthropic forced tool use, Gemini `responseSchema` | Schema violations structurally impossible during generation (constrained decoding) |
| 4. Validation + retry | A library (Instructor/Pydantic, Zod, Guardrails) checks output against your data model, re-prompts on failure with the specific error | Catches semantic correctness constrained decoding can't (e.g. a total that doesn't match its line items) |

**Common mistake:** trusting JSON mode alone as if it guaranteed schema correctness. It fixes "wrapped in prose" and "malformed syntax" almost completely, but leaves "wrong shape" and "hallucinated field" fully intact.

## Decision framework

| Situation | Recommended layer |
|---|---|
| Low-stakes internal script, occasional use | Prompt-level schema + JSON mode |
| Production pipeline feeding a database/downstream system | Native structured outputs |
| Customer-facing or financial data (invoices, billing, contracts) | Native structured outputs + validation-and-retry loop |
| Long documents with many extractable items | Chunked extraction + merge/dedup, on top of whichever layer fits the stakes |
| Self-hosted / open-weight models | Grammar-constrained decoding (Outlines, llama.cpp grammars) + validation |

## The schema-writing checklist

Before shipping any extraction schema, confirm:

- [ ] Every field is marked **required or optional** explicitly — don't leave the model to infer which fields matter
- [ ] Every field has a **short description** beyond its name — a field literally named `amount` with no description invites ambiguity (currency? tax-inclusive? which of three dollar figures?)
- [ ] Every fixed-value field uses an **enum** rather than free text (a `status` or `category` field) — this removes near-miss errors like "high" vs "High" vs "urgent"
- [ ] Nesting stays as **shallow** as the source content allows — reliability measurably degrades past 2–3 levels of depth
- [ ] **Null handling is stated explicitly** for every optional field (null vs. omit vs. sentinel value) — an unstated default is where hallucinated placeholder values come from
- [ ] At least one **semantic validation rule** checks correctness beyond shape — a sum that should match a total, a date within a plausible range, a required cross-field relationship

## Worked example

Support ticket: *"Hey, my order #48213 never arrived and it's been 9 days, I paid $64.99 for it and I'm getting pretty frustrated, can someone check on this or refund me?"*

Schema:
```json
{
  "order_id": "string",
  "days_since_order": "integer",
  "amount_paid": "number",
  "sentiment": "enum: neutral | frustrated | angry",
  "requested_action": "enum: refund | replacement | status_update | other"
}
```

Output:
```json
{"order_id": "48213", "days_since_order": 9, "amount_paid": 64.99, "sentiment": "frustrated", "requested_action": "refund"}
```

The enum constraints on `sentiment` and `requested_action` are doing the real reliability work here — converting free-form interpretation into a small, known set of routing-ready values.

## Few-shot examples still matter, even with a formal schema

A schema constrains *shape*, not *interpretation*. One or two worked examples still help resolve genuinely ambiguous judgment calls the schema can't express (e.g. which of two dates in a document is the relevant one, or what "net 30 from invoice date" means). Reserve examples for real ambiguity — don't pad the prompt with examples that only demonstrate formatting the schema already enforces.

## Failure modes to test for

- Model wraps JSON in an explanatory sentence or markdown fence
- Trailing comma / unescaped quote / missing closing brace
- Hallucinated field when a value is genuinely absent, instead of null/omission
- Right value, wrong type (a number as a quoted string, inconsistent date formats)
- Truncated array on long extraction tasks — silently dropping items rather than flagging it

## Evaluating extraction accuracy

- **Schema validity rate** — easiest to measure, least informative (should already be ~100% with native structured outputs)
- **Field-level precision and recall**, tracked separately, against a hand-labeled evaluation set (30–50 representative documents)
- **Hallucination rate** — how often the model invents a value with no support in the source — tracked as its own metric, not folded into general accuracy

## When NOT to over-engineer this

Schema-constrain exactly the fields a downstream system actually consumes, and leave everything else as optional free text or drop it entirely. Over-specifying a schema with fields nothing downstream reads adds failure surface for zero benefit. And genuinely open-ended tasks — summarization, brainstorming, exploratory analysis — lose value when forced into a rigid schema; don't route every LLM call through structured outputs on principle.
