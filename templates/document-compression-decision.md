# Document Compression Decision Guide

A bigger context window does not solve "the document is too long." Models degrade in accuracy as input length grows even well within the stated context limit ("context rot"), and information buried in the middle of a long input is retrieved far less reliably than information at the start or end ("lost in the middle"). Compression is about reliability, not just fitting text in.

Full guide with the research behind this: [How to Compress Long Documents for AI Without Losing Important Context](https://aihustleworld.com/2026/09/compress-long-documents-for-ai.html)

## The six approaches, and what each discards

| Approach | What survives | What's typically lost |
|---|---|---|
| Truncation | Content before the cutoff | Everything past the cutoff, regardless of relevance |
| Extractive summarization | Exact original wording of selected passages | Flow between passages; anything not explicitly selected |
| Abstractive summarization | General meaning, densely rewritten | Exact wording, qualifiers, precise numbers |
| Hierarchical / map-reduce | Section-level detail at each stage | Connections spanning multiple sections |
| Semantic chunking + retrieval | Content directly relevant to the query | Holistic view of the full document |
| Token-level compression (e.g. LLMLingua) | Structure and high-contribution tokens | Low-contribution words; some stylistic nuance |

**None of these is universally better.** Picking one technique and applying it to every document type regardless of what that document needs preserved is the most common mistake.

## Decision table

| Situation | Recommended approach |
|---|---|
| Document fits the context window, stakes are high | Send it uncompressed — don't compress by default |
| Legal, medical, or financial source material | Extractive summarization or targeted retrieval — avoid abstractive rewriting |
| General background or narrative content | Abstractive summarization for density |
| Document far exceeds the context window | Hierarchical / map-reduce, recursive if extremely long |
| Repeated narrow queries against a large corpus | Semantic chunking + retrieval |
| Already-compressed content needs further reduction | Token-level compression as a final pass |

## Choosing by document type

- **Legal contracts / regulatory filings** — extractive summarization or targeted retrieval; a paraphrased clause can shift its legal meaning
- **Codebases** — retrieve relevant functions/call sites/dependencies, don't summarize code as prose
- **Long chat/support transcripts** — hierarchical summarization that preserves chronology and decision points
- **Research papers / long reports** — hybrid: abstractive for background, extractive preservation for specific findings/statistics/methodology

## Worked example

40-page vendor contract, question: *"what are the termination conditions and notice period?"*

The defensible approach: semantic chunking of the contract into sections → retrieve the sections relevant to "termination"/"notice period" → present the **exact retrieved clauses**, not a paraphrase. This preserves the precise wording that determines the contract's legal effect while discarding the 38 unrelated pages.

## The hallucination risk compression introduces

Abstractive summarization can state something as fact that isn't actually in the source — especially numbers, dates, and causal claims smoothed over during rewriting. A hallucinated line reads exactly like every accurate line around it. **Mitigation:** pair abstractive summarization with a faithfulness check — a separate pass, or a rule-based check on numbers/named entities, confirming every specific claim traces back to the source.

## Preserving tables, code, and numbers

Generic prose-focused summarizers will describe a table in words instead of preserving it, and mangle code by treating it as prose to paraphrase. **Fix:** segment the document by content type *before* compression — extract tables/code/structured data and preserve verbatim or convert to a compact structured format; summarize prose separately.

## Evaluating a compression pipeline

Don't just measure "did it fit the token budget" — that's close to useless on its own (any method can hit a token target by discarding more). Measure instead:

- **Faithfulness** — does the compressed version make claims the source doesn't support?
- **Coverage** — does it retain the specific facts your downstream task actually needs (checked against a task-specific must-retain list)?
- **Task performance** — does the downstream task perform as well on compressed input as on the full source?

## When NOT to compress

- The document reasonably fits the context window and the task's stakes justify sending it whole (especially legal/contractual analysis where exact clause wording is the point)
- The task needs genuinely holistic understanding (overall tone, argument structure) rather than an answer to one narrow query — retrieval-based compression surfaces fragments, not the whole
- Query volume is low enough that the engineering cost of a real pipeline exceeds what it would ever save in tokens

## Cost math

A document compressed to a tenth of its original length costs roughly a tenth as much to process on every call that uses it — this compounds fast for any document queried more than once. The break-even favors compression whenever a document will be queried multiple times; a document queried once has a much weaker case for a sophisticated pipeline.
