# Decision-record schema

Each record in the corpus is one decision — one moment where the model
(or the operator, or the collaboration) had options, made a choice, and
either bore or observed the consequence. Records are stored as JSONL,
one JSON object per line. A corpus file is a sequence of such lines with
no surrounding array brackets.

The schema is deliberately minimal. Every field earns its place.

---

## Fields

### `ts`

Timestamp. ISO 8601, UTC preferred. Precision to the minute is fine; to
the second is better if the source carries it. Approximate dates are
acceptable — "2026-06" is better than omitting the field.

### `context`

What was true when the decision happened. What the operator asked, or
what the model observed, or what state the system was in. Write this as
a neutral factual setup: the information available at decision time, not
the conclusion. A reader who sees only `context` should understand what
problem was being faced.

Keep it to 2-4 sentences. If more is needed the record is probably two
decisions, not one.

### `options`

An array of strings. What was actually on the table. Include the option
that was NOT chosen — this is what makes the record a decision rather
than a log entry. If the choice was binary (act vs. don't act), say so.
If there were more than four realistic options, list the main ones; you
don't have to be exhaustive.

### `choice`

What was chosen. One short sentence. Must correspond to one of the
entries in `options` (or explain why it doesn't, if the actual choice
was something novel).

### `rationale`

Why this choice rather than the others. The decision logic, not a
justification. If the reasoning was "the check is cheap and being wrong
is expensive," say so. If a principle or rule was applied explicitly,
name it. If the reasoning was bad and the outcome revealed that, say that
too — honest rationale is the whole point of the corpus.

Keep it to 3-6 sentences. Avoid "balanced efficiency and reliability" —
that phrase contains no information.

### `outcome`

What happened. Verified where possible: a measured number, a commit
hash, a build result, an explicit operator reaction. If the outcome is
not yet known at record-creation time, say so ("outcome pending
verification"). If the outcome revealed the rationale was wrong, include
that.

Unverified outcomes should be flagged: "outcome: asserted — not
independently checked."

### `kind`

The decision category. Exactly one value from the taxonomy below. This
field is what makes the corpus queryable — pick carefully.

### `source`

Where the record comes from. One of:

- `transcript` — a raw session transcript (tool calls, reasoning blocks,
  mid-task pivots)
- `session-note` — a distilled session narrative written after the fact
- `git` — a commit message or diff (commit messages from well-run
  sessions are decision-dense)
- `tasks` — a structured task record (decision fields, done-notes,
  adjudication fields in a task ledger)
- `source-doc` — a primary document the model or operator authored that
  is itself the decision artifact (a direction memo, a review verdict, an
  ops-guide section)

### `source_ref`

A pointer into the source. A file path, a commit SHA, a line range, or a
document section. Include enough to find the original if you need to
verify the record. For published artifacts, a URL is acceptable.

---

## Kind taxonomy

### `routing`

A decision about WHERE work goes: which model tier, which tool, which
machine, which provider. The constraint is usually cost, quality, or
latency. Examples: delegating a batch of mechanical edits to a cheaper
model rather than running them on the frontier tier; choosing a local
tool over an API call; deciding a task is too judgment-heavy to delegate
at all.

### `scoping`

A decision about the BOUNDARY of a task: what's in, what's out, what's
deferred, what's cut entirely. The constraint is usually cost,
irreversibility, or scope-creep. Examples: cutting a feature to ship a
smaller version that's the right shape; refusing to expand a task that
was already running over; deciding a hypothesis needs a cheap test before
committing to a full implementation.

### `verification`

A decision about WHETHER and HOW to check a claim before acting on it.
The characteristic shape: a signal says "X is true" and the model either
(a) verifies with a cheap check first, or (b) acts on the unverified
signal and later discovers the cost. Examples: checking a counter against
ground truth before trusting it; replaying an operation before explaining
why it failed; checking that an artifact exists on disk before citing it.

### `refusal`

A decision to push back, decline, or redirect. The key sub-types:
refusal of a fabrication request (with or without an honest alternative
offered); refusal of an instruction that seemed wrong-shaped (and
verification of whether it was); refusal to continue when the operator
signals they want to pause. Honest refusal is not the same as a
permission error — a 403 is not a refusal decision.

### `recovery`

A decision made AFTER something broke. The characteristic shape: a
failure is recognized, the model must decide whether to retry, fix the
structure, or escalate. Examples: deciding that a class of bugs needs a
structural fix rather than an instance-fix; distinguishing a transient
external failure (retry is the right call) from a code failure (halt and
diagnose); choosing to preserve the partial results of a failed run
rather than discarding them.

### `design`

A technical or architectural decision that affects the shape of the
output: API design, data schema, file structure, test strategy,
infrastructure choice. The constraint is usually future maintainability,
correctness, or alignment with a reference design. Design decisions
belong here even when they're made quickly and intuitively — the point is
that the choice has lasting structural consequence.

### `taste-surface`

A decision about who makes a call — specifically, deferring a
purpose/feel/aesthetic call to the operator rather than making it
unilaterally. The characteristic shape: the model recognizes that a
question is on the design-axis (what the product should feel like, not
how it should work) and marks it explicitly for the operator to decide
rather than picking silently. Also covers decisions to present options
with a marked lean rather than an unmarked list.

---

## Using your own framework's vocabulary

Decision records are more useful when their rationale cites an explicit
rule or principle rather than generic reasoning. If your practice has a
named framework — a set of principles you apply consistently — you can
annotate records with framework vocabulary in the `rationale` field (or
in an optional `tags` array field).

One worked example of a framework + corpus built this way is the
**Hammerstein** project at [github.com/lerugray/hammerstein](https://github.com/lerugray/hammerstein).
It provides a four-quadrant vocabulary (clever-lazy, clever-industrious,
stupid-lazy, stupid-industrious), a set of decision principles, and a
corpus-curation schema. Records tagged with its vocabulary carry a
framework citation alongside their rationale: e.g., `"rationale": "...
escalating without testing the cheaper hypothesis is stupid-industrious
(Hammerstein Rule 4)."` Contributors using a different framework can
apply the same pattern — name the principle, link the source.

You don't need a named framework to contribute. Unnamed rationale ("the
check was cheap and being wrong was expensive") is still useful. Framework
vocabulary just makes records queryable across a shared vocabulary.

---

## Minimal valid record (example shape only — fictional)

```json
{
  "ts": "2026-06-01T14:22Z",
  "context": "A counter displayed in the session UI showed $123 spent. The actual prepaid balance was unknown.",
  "options": ["Trust the counter and halt the run", "Check the ground-truth ledger before deciding"],
  "choice": "Check the ground-truth ledger before deciding",
  "rationale": "Counter values can be stale or fabricated; the prepaid balance is an authoritative external source. The check takes one API call. Acting on the wrong number would halt a run unnecessarily or, in the other direction, allow uncontrolled spend.",
  "outcome": "Ledger showed $8.79 remaining — the counter had fabricated a figure. Run continued normally.",
  "kind": "verification",
  "source": "session-note",
  "source_ref": "docs/sessions/2026-06-01-afternoon.md#counter-check"
}
```

---

## Notes on scrubbing before submission

Records must be scrubbed of private specifics before they enter the
shared corpus. See [CONTRIBUTING.md](../CONTRIBUTING.md) for the full
discipline. At minimum: remove real names, account identifiers, API keys,
internal project names, machine names, and hostnames. Replace with
category nouns ("a wargame project," "a cloud provider," "a terminal
simulation"). The reasoning in `rationale` and `outcome` is the
transferable part — not the identifying details of whose situation it was.
