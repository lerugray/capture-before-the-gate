# corpus/

The decision-record corpus and its structure.

## Files

- **`SCHEMA.md`** — the decision-record schema (the fields, the `kind` taxonomy, the conventions). Read this first.
- **`CURATION.md`** — how a record moves from auto-distilled (low confidence) to curated (high confidence), and the quality gate it has to pass.
- **`sample-records.jsonl`** — **211 real decision records from the Fable 5 era**, distilled from transcripts and then scrubbed and genericized. Every name, path, key, hostname, and private project identifier has been removed or replaced with a category noun; the decision, the options weighed, the rationale, and the outcome are preserved. This is what the method actually produces. The kinds break down roughly: design 62, recovery 48, verification 35, routing 22, scoping 22, refusal 22.
- **`EXAMPLE-ENTRIES.md`** — three *invented*, annotated records that teach the schema field-by-field. Use these to learn the format; use `sample-records.jsonl` to see it at scale.

## What is not here, on purpose

The raw transcripts are not in this repository and never will be. Raw model
transcripts contain API keys, environment dumps, and personal content surfaced
in tool outputs — publishing them would leak credentials and private data. The
records here are the *distilled, scrubbed* layer: the judgment, without the
exhaust. See `../METHOD.md` Step 1 and `CURATION.md` for the scrub discipline.

## Contributing your own

See [`../CONTRIBUTING.md`](../CONTRIBUTING.md). The short version: capture your
own model's doctrine with the method, format records to `SCHEMA.md`, scrub your
own private data (the gate is yours to run before you submit), and open a PR.
