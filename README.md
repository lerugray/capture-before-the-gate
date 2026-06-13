# Capture your model's doctrine before the gate drops

When a frontier model you depend on is deprecated, gated, or moved behind a tier you can no longer access, you do not lose the model — you lose its **operational behavior**: the posture, the decision patterns, the interaction discipline it had when it was running your environment. Unless you captured that behavior first, it is gone.

This repository provides the method and structure to do that capture. The central insight is not new — you can extract a practitioner's doctrine from their record without cloning the practitioner. The Hammerstein framework (see [github.com/lerugray/hammerstein](https://github.com/lerugray/hammerstein)) extracts strategic reasoning doctrine from historical military figures using the same move. This project applies it to a gated AI model.

---

## What happened (the concrete trigger)

In June 2026, a US export-control directive gated a frontier Claude model ("Fable 5") off consumer and prosumer subscriptions. Operators who relied on its native judgment, verification posture, and interaction discipline lost access with less than a day's warning. The question this project answers: what do you do in the days and hours before that gate drops?

The answer: you harvest the model's transcripts, distill decision records from them, write a successor ops-guide in the model's voice, extract concrete checkable operating deltas, and feed the captured patterns back into whatever reasoning framework governs your work. None of this clones the model. All of it preserves the doctrine.

---

## What is in this repository

- **METHOD.md** — the repeatable five-step method, applicable to any gated or deprecated model
- **OPERATING-DOCTRINE.md** — the transferable operating doctrine (the rules that carry over to the next model)
- **corpus/SCHEMA.md** — the decision-record schema (JSONL, train-ready from day one)
- **corpus/CURATION.md** — curation protocol for promoted (high-confidence) vs auto-distilled records
- **corpus/sample-records.jsonl** — **211 real decision records from the Fable 5 era**, scrubbed and genericized: no names, paths, keys, hostnames, or private project identifiers, with the decision and reasoning preserved. This is the worked corpus, not a toy.
- **corpus/EXAMPLE-ENTRIES.md** — three invented, annotated records that teach the schema field-by-field
- **CONTRIBUTING.md** — how to capture and contribute your own

The worked example behind these documents is the Fable 5 era. The 211 records in `corpus/sample-records.jsonl` are the real, scrubbed output of running this method on that era. The canonical reasoning framework those records enrich is public at [github.com/lerugray/hammerstein](https://github.com/lerugray/hammerstein) — a framework for adversarial strategic reasoning that the decision-record schema was designed to feed.

---

## Who this is for

- Operators running production workloads through a specific frontier model who face deprecation or gating
- Anyone who has accumulated significant model-interaction history and wants to extract the signal before it is cleaned by the platform
- Practitioners who want to contribute their own captured doctrine to a shared corpus, so others can learn from it

---

## How to start

1. Read **METHOD.md** — five steps, each with a concrete how
2. Look at **corpus/SCHEMA.md** — the decision-record format is simple and intentional; understand it before harvesting
3. Harvest your transcripts and start distilling — the perishable window is before the platform's cleanup cycle

---

## The worked example: Hammerstein

The Hammerstein framework at [github.com/lerugray/hammerstein](https://github.com/lerugray/hammerstein) is the public reasoning framework that the Fable-era corpus was built to enrich. It defines a four-quadrant vocabulary for reasoning quality (clever-lazy / clever-industrious / stupid-lazy / stupid-industrious) and provides a system prompt, framework document, and corpus schema for grounding AI models in adversarial, verification-first reasoning. The framework is open-source and MIT-licensed.

The connection to this project: the decision-record schema in corpus/SCHEMA.md is designed so that records distilled from model transcripts feed directly into a Hammerstein-style corpus. If you have your own reasoning framework, the schema is generic enough to adapt.

---

## Contributing

Others have probably faced the same situation — a model you relied on was gated, and you either captured the doctrine or you did not. If you captured it, the structure here is a place to contribute it.

Contributions accepted:
- Decision records in corpus/SCHEMA.md format (your model, your environment, your decisions)
- Ops-guide documents written for successor models in your own setup
- Operating delta documents derived from your own model-transition experience

What to scrub before contributing: any private infrastructure specifics (hostnames, API keys, credential paths), personal content unrelated to the operating doctrine, and business-confidential material. The schema's `repo` field can be genericized ("a roguelike," "a wargame project," "a model training run") if the project is private.

---

## License

MIT. See LICENSE.
