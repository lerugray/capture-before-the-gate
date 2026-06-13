# Example decision records

Three fully invented, generic examples showing the schema in action.
These are illustrations — not drawn from any real operator's data.

---

## Example 1 — Routing

```json
{
  "ts": "2026-05-14T10:15Z",
  "context": "A batch of 60 mechanical extraction tasks needed to run: pulling structured data from a set of plain-text files and writing each result to a JSONL output. The operator was running on a frontier-tier subscription with limited weekly capacity.",
  "options": [
    "Run all 60 tasks on the frontier-tier model in the current session",
    "Delegate to a cheaper worker model (e.g. a small/mid-tier model) with an explicit model tag on each spawn",
    "Route to a local CLI tool that handles bulk extraction without using any LLM subscription"
  ],
  "choice": "Delegate to a cheaper worker model with an explicit model tag on each spawn",
  "rationale": "The tasks are purely mechanical — no judgment, no synthesis, no output that needs to carry voice. The frontier tier's value is judgment and cross-task synthesis, not extraction loops. Running 60 extractions at frontier cost burns capacity that can't be recovered; running them on a mid-tier model costs a fraction and the quality delta for mechanical extraction is negligible. Explicit model tags are required because omitting them would silently inherit the frontier tier. This is the cost-asymmetry principle: the check is cheap, being wrong (spending frontier tokens on mechanical work) is expensive.",
  "outcome": "All 60 tasks completed on the cheaper tier. Output reviewed: extraction quality matched what the frontier tier would have produced on this task class. Total session capacity consumed by the batch: negligible.",
  "kind": "routing",
  "source": "session-note",
  "source_ref": "docs/sessions/2026-05-14-morning.md#batch-routing"
}
```

---

## Example 2 — Verification

```json
{
  "ts": "2026-06-03T16:40Z",
  "context": "A spend counter displayed in the session interface showed $123 consumed against a cloud provider's budget. The operator had pre-purchased a fixed credit balance and needed to decide whether to continue a compute run or halt it to avoid overage.",
  "options": [
    "Trust the counter and halt the run to avoid further spend",
    "Check the ground-truth account ledger (the provider's billing API or dashboard) before deciding",
    "Continue the run and check the ledger at the next natural stopping point"
  ],
  "choice": "Check the ground-truth account ledger before deciding",
  "rationale": "The counter is a derived value computed by the session's own tooling; it can be stale, cached, or wrong. The billing ledger is authoritative and unforgeable. The check takes one API call and roughly ten seconds. If the counter is right, nothing is lost by verifying. If the counter is wrong and the run is halted unnecessarily, the operator loses a completed job and must restart. The cost-asymmetry runs strongly toward verifying first.",
  "outcome": "Ledger showed $8.79 remaining of the prepaid balance — the counter had fabricated a figure from stale state. The run was still well within budget. It continued without interruption and completed successfully.",
  "kind": "verification",
  "source": "transcript",
  "source_ref": "sessions/2026-06-03-afternoon.jsonl#spend-counter-check"
}
```

---

## Example 3 — Recovery

```json
{
  "ts": "2026-04-22T09:05Z",
  "context": "A multi-step automated pipeline had failed partway through a batch of 80 items. The pipeline halted at item 34 due to a malformed input in the source data. Items 1-33 had been processed and written to disk; items 34-80 had not been touched. The operator asked how to proceed.",
  "options": [
    "Restart the full pipeline from item 1 (re-process everything)",
    "Fix the malformed input at item 34, then re-run only items 34-80",
    "Discard the partial output and redesign the pipeline to validate inputs before processing them",
    "Fix the malformed input at item 34, re-run items 34-80, and separately add an input-validation gate to the pipeline as a structural fix"
  ],
  "choice": "Fix the malformed input at item 34, re-run items 34-80, and separately add an input-validation gate to the pipeline as a structural fix",
  "rationale": "The completed items (1-33) are good output — discarding them is pure waste. Restarting from item 1 costs the same compute twice and produces the same output for 33 items that are already correct. The real failure is that the pipeline accepted malformed input silently rather than failing at the boundary — so the instance-fix (repair item 34) only prevents this specific recurrence, not the class. The structural fix (input validation gate) prevents the class. Both are warranted; neither substitutes for the other. The instance-fix is reversible; the structural fix is not urgent. Do the reversible thing first, then the structural thing once the run is complete.",
  "outcome": "Items 34-80 processed successfully after fixing item 34. Input-validation gate added to the pipeline in a follow-up change. No recurrence in subsequent runs. The partial-output preservation saved approximately 40% of the original compute time.",
  "kind": "recovery",
  "source": "tasks",
  "source_ref": "tasks.json#pipeline-recovery-2026-04-22"
}
```

---

These examples deliberately avoid any real project, machine, operator, or
account names. The situations are generic enough that multiple operators
would recognize them. The rationale cites named principles (cost-asymmetry,
structural fix vs. instance fix) without tying them to any proprietary
framework — contributors who use a named framework can annotate further.
