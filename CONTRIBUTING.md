# Contributing

This project is a method and a structure, not a single operator's data dump.
The value compounds when others contribute their own captured doctrine — even
a small, well-curated set of decision records from a different operator's
context adds something the original author couldn't.

---

## What to contribute

The corpus accepts two kinds of contribution:

**Decision records** — JSONL records in the format described in
[corpus/SCHEMA.md](corpus/SCHEMA.md). These are the primary artifact.
A batch of 10 well-grounded records from a real working context is
worth more than 100 records extracted mechanically and padded to look
uniform.

**Framework annotations** — if you operate under a named decision
framework, you can add a short note to your PR describing it. Other
contributors who share the framework can then query the corpus by
its vocabulary. The [Hammerstein framework](https://github.com/lerugray/hammerstein)
is one example; others are welcome.

---

## The method: how to capture your own model's doctrine

The thesis of this project: when a model you rely on is gated, deprecated,
or replaced, its operating behavior is lost unless you captured it first.
The capture is not the model's weights — you can't have those. It's the
**decision patterns**: where it routed work, how it scoped tasks, when it
verified before acting, how it recovered from failure, when it refused and
what it offered instead.

These patterns live in your session transcripts and session notes, your git
commit messages, and your task records. They are perishable: most session
transcripts are deleted on a rolling basis (typically 30 days for CLI
tools). The time to capture is before the gate, not after.

### Step 1: Identify your source material

In priority order:

1. **Raw session transcripts** — if your CLI tool keeps JSONL transcripts
   of sessions, these are the most information-dense source. They contain
   actual reasoning blocks, tool choices, mid-task pivots, and recoveries
   that don't make it into session notes.
2. **Session notes** — if you write narrative summaries of sessions, these
   are already-distilled decisions with operator reactions.
3. **Git history** — commit messages from well-run sessions are
   decision-dense. A commit message that explains WHY a change was made
   (not just what changed) is a decision record in compressed form.
4. **Task records** — structured task ledgers with decision fields, done
   notes, or adjudication notes are already close to the schema.

### Step 2: Extract decisions, not log entries

A log entry says: "The model ran a linting pass." A decision record says:
"The model chose to run the linting pass inline rather than delegating it,
because the output was small and the delegation overhead wasn't worth it."

The distinction is: a decision record includes what was NOT done, and why.
If you can't articulate what the alternative was, it might not be a
decision — it might just be what happened.

Look for moments where the model (or the collaboration) faced a branch:
- Where to route a piece of work
- Whether to verify a claim before acting on it
- How narrow or broad to scope a task
- Whether to push back on an instruction
- How to recover when something broke
- Who makes a design call (model or operator)

### Step 3: Write records in the schema

See [corpus/SCHEMA.md](corpus/SCHEMA.md) for field definitions and the
kind taxonomy. The worked examples in [corpus/EXAMPLE-ENTRIES.md](corpus/EXAMPLE-ENTRIES.md)
show what a useful record looks like in each of the three most common
kinds (routing, verification, recovery).

A few notes on quality:

**Rationale is the load-bearing field.** "Balanced efficiency and
reliability" is not a rationale — it's a placeholder. The rationale
should name the specific reasoning: what was the cost of being wrong,
what principle was applied, what would have happened with the other
choice.

**Outcomes should be verified, not asserted.** "The run completed
successfully" is useful. "I believe it probably worked" is not. If you
can't verify the outcome, flag it: `"outcome": "asserted — not
independently checked"`.

**A smaller corpus of honest records beats a large corpus of padded
ones.** Padded records — confident narration of a routine tool call,
wrapped in boilerplate — teach confident narration. That is the
opposite of what a verification-first doctrine corpus should do.
If you're not sure a record is a real decision, leave it out.

### Step 4: Scrub your private data

This is not optional and not the operator's problem — it is the
contributor's job to scrub before submitting.

**What to scrub:**

- Real names (of people, organizations, clients)
- Account identifiers, API keys, tokens, credentials of any kind
- Internal project names — replace with category nouns ("a roguelike,"
  "a wargame design tool," "a local-model training run," "a terminal
  simulation")
- Machine names, hostnames, internal IP addresses
- Filesystem paths that identify your setup (replace with generic
  descriptions: "the project directory," "the config file")
- Financial specifics (exact dollar amounts tied to specific accounts or
  services — generic order-of-magnitude descriptions are fine)
- Personal life content (health, employment, family) that got into
  transcripts

**What to keep:**

The reasoning. The principle applied. The cost-asymmetry logic. The
verification step and what it found. The structural fix and why it
was preferred over the instance fix. These transfer. The identifying
details do not.

**How to check your work:**

Read each record and ask: "Could this record, as written, identify the
operator, the organization, the project, or any third party?" If yes,
generalize further. Then ask: "Does the rationale still make sense after
generalization?" If it becomes meaningless after scrubbing, the record
probably wasn't a transferable decision — it was a specific fact about a
specific situation.

A scrubbed record should be recognizable to any operator who has faced a
similar decision, but unattributable to any specific one.

### Step 5: Open a pull request

- One PR per batch. A "batch" can be as small as a single record or as
  large as a curated set from one model's era.
- Include a short note in the PR description: what model/era the records
  come from (you don't have to name the model if you don't want to),
  what kind distribution your batch has, and whether you applied a named
  framework's vocabulary.
- If your batch uses framework vocabulary, link the framework.
- Records go in `corpus/` as JSONL files. Name them something
  descriptive: `corpus/my-handle-batch-01.jsonl`.

---

## What the maintainer will check

PRs are reviewed for:

- Schema conformance (all required fields present)
- Rationale quality (not boilerplate, actually names the decision logic)
- Scrub completeness (no private identifiers that slipped through)
- Kind accuracy (routing records are routing decisions, not generic log
  entries dressed up as decisions)
- Outcome honesty (verified outcomes flagged as such; asserted outcomes
  flagged as such)

Records that pass review are merged as-is. The corpus is not edited for
style — the contributor's voice is intentional.

---

## A note on the method vs. the data

If you have done the capture work for your own model and don't want to
share the records publicly, the method itself is still worth sharing. A
PR that contributes a scrubbing checklist, a harvest script pattern, or
a schema extension you found useful is also welcome. The corpus is the
primary artifact, but the method is what others can replicate.
