# The method: capture your model's operating doctrine before access is lost

This is the repeatable procedure. It was developed under time pressure — less than a day of warning before a frontier model was gated — but it works best when you start it before the timeline is certain. Do not wait for a confirmed deprecation date.

Each step explains what it is, why it matters, and how to do it concretely.

---

## Step 1: Harvest transcripts before the platform cleans them

**What it is.** Raw model-interaction transcripts are the gold. They contain actual reasoning blocks, tool-choice sequences, mid-task pivots, and recovery moves — the behavioral record, not a summary of it.

**Why it matters.** Platform cleanup is real. Claude Code, for example, cleans project transcripts after roughly 30 days. If the model is gated before you harvest, and the platform cleans transcripts before you notice, the behavioral record is gone. The harvest is perishable in a way the model's weights are not; weights can sometimes be retrieved or accessed by others; your specific interaction history cannot.

**How.**

- Locate your platform's raw transcript store. For Claude Code, this is `~/.claude/projects/*/*.jsonl`. For other platforms, find the equivalent — conversation exports, API logs, session archives.
- Copy them to a local staging directory that is **gitignored**. Raw transcripts contain environment dumps, API tokens surfaced in tool outputs, and personal content in tool results. They must never be committed anywhere without scrubbing.
- Run an idempotent harvest script so you can sweep again as new transcripts arrive before the gate fully drops. The script should filter by model identifier and era boundary (a start date after which the target model was in use).
- On multi-machine setups: each machine holds its own transcript store. Harvest all of them. Do not assume one machine's store is complete.

**What you are harvesting for.** Not the raw content — the raw transcripts are too noisy for direct use. You are harvesting so you have the primary source for distillation in Step 2.

---

## Step 2: Distill decision records from the raw material

**What it is.** A decision record is a single JSONL entry capturing one decision: the context, the options considered, the choice made, the rationale, and the outcome where verifiable. The schema is in **corpus/SCHEMA.md**.

**Why it matters.** Raw transcripts are large, noisy, and full of low-information tool-call narration. A model that read only the raw transcripts would learn confident procedure-following, not the decision texture you are trying to preserve. Distillation separates the signal — actual judgment calls with rationale — from the filler.

The key quality distinction from a real corpus run: roughly 73% of auto-distilled transcript records were low-information narration wrapped in interchangeable boilerplate. Only about 15-20% were actually rule-bearing. If you train on the bulk without filtering, you teach confident narration, not judgment. The curated records — those with a named decision, an explicit rationale, and a verifiable outcome — are a small fraction of the total and carry almost all the value.

**How.**

- Run an auto-distiller over your harvested transcripts to produce a first pass. Any LLM can do this; route it to a cheap tier. The goal is a first cut in the decision-record schema, not a finished corpus.
- Apply a quality gate: for each record, ask whether it names a specific choice (not just "used tool X"), states a rationale (not just "for efficiency"), and has an outcome that is checkable. Records that fail this gate go into a low-confidence bucket, not the main corpus.
- **Promote records to high-confidence status interactively**, with the operator reviewing. Do not automate promotion — the curation pass is where the operator's judgment about what was real is load-bearing. Corpus curation protocol is in **corpus/CURATION.md**.
- Additional sources beyond transcripts: session notes written during the model era (already-distilled narrative), git commit messages from that era (decision-dense, authored under the model's guidance), and structured decision fields in any task-management records your setup maintained.
- Scrub before committing. Run key-pattern regexes over every record before it touches version control. Anything that matches an API key, token, hostname, or personal-data pattern must be caught and removed.

**The kind taxonomy.** The schema's `kind` field is not decoration — it tells you what type of decision to expect in the record and lets you query by decision type later. The taxonomy from the worked example: `routing` (where work goes), `recovery` (failure handling), `verification` (checking before acting), `design` (structural/architectural calls), `scoping` (cut/keep under pressure), `refusal` (pushing back on a bad premise), `taste-surface` (handing a design-axis call to the operator). Use this taxonomy or adapt it to your environment, but be consistent.

---

## Step 3: Write a successor ops-guide for the model that inherits the seat

**What it is.** A document written from the gated model's perspective — in that model's voice, based on the captured decision records — addressed to the model that replaces it. It explains the operating doctrine, the patterns that worked, and the failure modes to avoid, grounded in the specific environment rather than in generic advice.

**Why it matters.** The decision records capture individual decisions. The ops-guide captures the spine that connects them — the doctrine that produced the pattern, stated as operational rules a successor can apply from session one. Without it, the successor model starts from scratch and rediscovers the same patterns through the same failures.

The most important thing an ops-guide can do is name the **failure modes**: not what the gated model did right (this tends to be overrepresented in self-authored docs), but what cost real resources and how a successor should handle those situations differently.

**How.**

- Have the outgoing model write the first draft while it is still accessible. This is the ideal — the model can draw on its own in-context history and the decision records simultaneously. If the gate drops before this happens, reconstruct from the records.
- Structure around: (1) the one-paragraph version of the doctrine, (2) the economic rules (where work routes, what the expensive tier is for), (3) the orchestration patterns that earned their keep, (4) verification discipline grounded in specific failure examples, (5) honest capability notes — what the outgoing model handled that the successor should re-calibrate before trusting.
- The honest capability section is load-bearing. A model writing its own ops-guide has obvious incentive to present its tenure favorably. Counter this by requiring that every performance claim cite a measured outcome (a number, a commit, an on-disk artifact), not an impression.
- Ground the routing doctrine in the specific subscription and tool setup. Generic advice ("route work down") is less useful than "here is the specific provider tier hierarchy this environment uses and here is why."

---

## Step 4: Extract concrete, checkable operating deltas

**What it is.** The gap between the outgoing model's behavior and the successor model's default behavior, expressed as structural gates — triggers and required checks — not as dispositions ("be more careful").

**Why it matters.** Dispositions do not fire. Telling a model to "verify more" produces no change in behavior because the model already believes it verifies. Structural gates fire on specific trigger conditions and require specific actions before proceeding. The difference is the difference between advice and a rule.

The clearest pattern from the worked example: the successor model produced four failures within hours of inheriting the seat, all reducible to a single pattern — asserting a conclusion from filenames or priors when one cheap read-only check would have established the actual state. The fix was not "verify more." It was:

- **No deletion/move verb on a named path without a read-only inspection reported in the same turn, before the recommendation.** Trigger: deletion verb + a path. Required: open or list the path first and state what it actually is.
- **No proposed test or experiment without an inline prior-art check.** Trigger: "we should run / let's test." Required: one clause reporting a search for a prior run of the same test — "checked: no prior X" or "prior X exists here."
- **On "take stock / hold / pause," switch to hold-posture: close open items, produce a settled-state summary, generate zero new threads until the operator re-opens.** The mirror failure of wrapping up early.
- **No value-verdict on an artifact not opened in the current session.** Required: read a representative sample first; the verdict comes after and cites what was read.

**How.**

- Start from the failure record. What did the successor get wrong in the first sessions? What would the outgoing model have done differently, and what specific check would have caught it?
- Express each delta as a trigger condition and a required action, not as a principle. Test each delta by asking: can I grep the model's output for the trigger and verify the required action was taken? If no, the delta is not structural enough.
- Record these in the ops-guide (Step 3) AND as a standing rule in whatever cross-session rule system your environment uses. Deltas that live only in a document get lost; deltas that are auto-loaded into every session fire.

---

## Step 5: Feed the captured patterns back into your reasoning framework

**What it is.** Promoting the clearest decision records from the corpus into whatever formal reasoning framework governs your work — whether that is a published framework like Hammerstein, an in-house system prompt, a retrieval corpus, or a curated few-shot library.

**Why it matters.** The decision records are raw material. A reasoning framework is the processing layer that makes the patterns generalize and persist. A record sitting in a JSONL file is useful for retrieval ("what did the model do in situation X"); a record promoted to the framework corpus is useful for every future judgment call that touches the same class of situation.

**How.**

- Select the highest-confidence records — the ones with a named decision, a clear rationale, an outcome, and a falsification (a case where the alternative would have failed). These are the candidates for framework promotion.
- Match each candidate to an existing framework entry. If the candidate covers the same principle as an existing entry but adds a new surface or counter-observation, promote it as an extension. If it covers a gap — a principle the framework implies but never states — promote it as a new entry.
- The most valuable candidates from any model-capture pass are typically: (a) the first positive exemplar of a behavior the framework only documents as a failure (e.g., refusal-with-pathway as a success, not just fabrication as a failure); (b) instances where the behavior operated at a scale or in a context the original corpus did not cover; (c) calibration data on where the framework wrapper adds lift vs. where the base model already occupies the target register.
- **Gate on quality, not quantity.** A large bulk corpus of auto-distilled records teaches confident narration, not judgment, if the signal density is low. Only promote records that would survive a "study before sentence" review — where you have read the record, verified the outcome, and can state specifically what decision principle it illustrates.
- If your framework is public (like Hammerstein), run the promoted candidates through an interactive curation pass with the operator before committing. Corpus quality is the framework's load-bearing property; bad entries teach the wrong thing.

---

## What this does not do

This method does not clone a model. A corpus of captured decisions preserves patterns, not capability. The honest claims: decision patterns transfer to prompts and harnesses; decision-shaped records can train a small-model lens for retrieval; a curated corpus answers "what did the model do in situation X and why" after the gate. It does not answer "what would the model do in situation Y it has never seen." For that, there is no substitute for the model itself — which is why doing the capture while access is still open is the load-bearing constraint.

The method also does not replace a successor model's own judgment. The operating deltas, the ops-guide, and the curated corpus give the successor a starting posture and a set of structural gates. They do not transfer the outgoing model's full decision space. Expect the successor to diverge, and use the framework to calibrate the divergence rather than to prevent it.
