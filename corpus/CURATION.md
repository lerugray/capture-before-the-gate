# Curation protocol

How a decision record moves from raw output to a record you would trust in a
corpus. The schema (`SCHEMA.md`) defines the shape; this defines the gate.

## Two confidence tiers

- **Auto-distilled (low confidence).** The first pass: an LLM reads the
  harvested transcripts and emits records in the schema. Useful for retrieval
  and volume, but a large fraction is low-information narration of a single
  tool pick wrapped in interchangeable boilerplate ("balances efficiency and
  reliability"). Do not train on this tier unfiltered — it teaches confident
  procedure-following, not judgment.
- **Curated (high confidence).** Records that pass the quality gate below and
  an interactive review. These carry almost all the value and are the only tier
  fit for promotion into a reasoning framework.

## The quality gate

A record is curation-worthy only if it has all of:

1. **A named decision** — a specific choice, not "used tool X" or "proceeded."
2. **An explicit rationale** — the operative reason, not "for efficiency."
3. **A verifiable outcome** — checkable against something external (a commit
   hash, a measured number, an on-disk artifact, an operator confirmation),
   not the model's own confidence.
4. **(Ideally) a counter-observation** — the condition under which the rule
   would not apply, or the failure mode it can over-fire into. A rule with no
   stated limit is usually under-examined.

A record that fails the gate goes to the low-confidence bucket. It is not
deleted — it has retrieval value — but it does not get promoted.

## Promotion is interactive, not automated

Promoting a record to high-confidence is a judgment call about what was
*actually* real, and it stays with the operator. Do not automate it. The
review pass is cheap (read the record, confirm the outcome, name the principle
it illustrates) and it is where the operator's knowledge of what happened is
load-bearing. A reviewer who finds nothing to reject is a suspect reviewer —
the gate proves its value by what it turns away.

## Tagging

Tag every curated record with its `kind` (see `SCHEMA.md`). If you run a
formal reasoning framework, add that framework's own vocabulary as a second
tag, so records can be queried both by decision type and by framework concept.
Keep tags consistent — inconsistent tagging defeats later retrieval.

## Scrub before commit

Every record that touches version control passes a secret-pattern check first
(API keys, tokens, hostnames, credential paths) and a personal-data check.
Raw transcripts never get committed; only scrubbed records do. See
`../CONTRIBUTING.md` for the contributor scrub checklist.
